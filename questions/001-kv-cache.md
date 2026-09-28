---
number: 001
title: 为什么 LLM 推理的显存瓶颈在 KV Cache？
category: 推理优化
tags: [kv-cache, inference, mqa, gqa, paged-attention]
difficulty: ⭐⭐⭐
date: 2026-09-28
---

## 问题

模型权重只有 140 GB，单卡 8×80 GB 完全装得下。为什么上下文开到几万 token 就 OOM？

## 一句话回答

**权重是常数，KV Cache 是变量。** 它随 `序列长度 × 并发数` 线性增长，而权重不变 —— 长上下文推理时，KV Cache 会远远超过模型本身。

## 展开

### 1. 算一算它到底多大

单条序列的 KV Cache：

```
2 × num_layers × num_kv_heads × head_dim × seq_len × dtype_bytes
```

以 Llama-3-70B 为例（80 层，8 个 KV head，head_dim=128，FP16）：

```
2 × 80 × 8 × 128 × 2 bytes = 327 KB / token
```

| 上下文 | 单条 KV Cache | 8 路并发 |
|--------|--------------|---------|
| 4K | 1.3 GB | 10.5 GB |
| 32K | 10.5 GB | 84 GB |
| 128K | 42 GB | **336 GB** |

模型本身 140 GB，128K 上下文下光 KV 就要 336 GB —— 这是 OOM 的真正来源。

### 2. Prefill 和 Decode 为什么表现不同

- **Prefill**：一次性处理整个 prompt，算力瓶颈（compute-bound），KV 一次性写入
- **Decode**：每次只生成 1 个 token，**访存瓶颈**（memory-bound）—— 每生成一个 token 都要把整个 KV Cache 读一遍

所以 decode 阶段的吞吐几乎完全由 KV Cache 的读带宽决定。

### 3. MQA / GQA 怎么省

MHA 里每个 Query head 都有自己的 K/V head。MQA 让所有 Query head **共享一组** K/V，GQA 折中 —— 分组共享。

```
MHA:  Q 32 组  K/V 32 组   → 1×
GQA:  Q 32 组  K/V  8 组   → 1/4
MQA:  Q 32 组  K/V  1 组   → 1/32
```

Llama-3-70B 用的就是 GQA（8 组），这已经是它能在 128K 下跑起来的前提。

### 4. PagedAttention 解决的不是"总量"

这是个常见误解。vLLM 的 PagedAttention **不减少 KV Cache 总大小**，它解决的是**碎片化**：

- 传统做法：按 `max_seq_len` 预分配连续显存 → 实际用了 30%，浪费 70%
- PagedAttention：像操作系统分页一样，按 block 分配 → 利用率从 ~20% 提到 90%+

配合 **Prefix Caching**（共享相同 system prompt 的 KV），共享前缀只存一份，这才是真正的"省"。

### 5. 其他路径

| 手段 | 思路 | 代价 |
|------|------|------|
| 量化 KV（FP8/INT8） | 直接砍字节数 | 精度损失，长上下文更敏感 |
| 滑窗 / 稀疏 Attention | 只保留部分 KV | 丢失长程依赖 |
| 分层淘汰（H2O 等） | 按注意力权重淘汰 | 实现复杂，效果依任务 |
| 投机解码 | 不减 KV，提升计算效率 | 不改显存瓶颈 |

## 面试延伸

- **如果并发从 8 提到 64，会发生什么？** KV Cache 直接 ×8，必须降上下文或上量化
- **为什么训练不需要 KV Cache？** 训练时所有 token 并行计算，KV 是中间激活，用完即弃
- **Prefix Caching 什么场景最赚？** system prompt 极长且高度重复 —— 比如 Agent 场景里固定的工具定义

## 延伸阅读

- vLLM 论文：*Efficient Memory Management for Large Language Model Serving with PagedAttention* (SOSP 2023)
- GQA 论文：*GQA: Training Generalized Multi-Query Transformer Models* (EMNLP 2023)
