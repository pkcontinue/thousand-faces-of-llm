---
number: 002
title: RoPE 为什么外推失效？NTK-aware 和 YaRN 在做什么？
category: 模型架构
tags: [rope, position-encoding, long-context, yarn, ntk]
difficulty: ⭐⭐⭐⭐
date: 2026-09-28
---

## 问题

模型训练时上下文 4K，想直接推理到 128K。为什么把 RoPE 的 base 从 10000 调大就能"免费"扩上下文？NTK-aware、YaRN 又分别解决了什么？

## 一句话回答

**RoPE 靠旋转角度编码位置，训练时没见过的角度会导致 Attention 打分失准。** 各种外推方法本质上都是在"把没见过的角度压回见过的范围"，区别只在于压缩方式够不够平滑。

## 展开

### 1. RoPE 是怎么编码位置的

对第 `i` 维，RoPE 把 Query/Key 旋转一个角度：

```
θ_i(pos) = pos × base^(-2i/d)
```

- **低频维度**（i 小）：旋转慢，负责远距离区分
- **高频维度**（i 大）：旋转快，负责近距离精细区分

### 2. 外推失效的根因

训练用 4K，意味着模型只见过 `pos ∈ [0, 4096)` 的旋转角度。

推到 128K 时，**高频维度**会转过好几整圈 —— 出现**混叠（aliasing）**：`pos=100` 和 `pos=100+2π/θ` 旋转到几乎相同的角度，模型分不清这是两个位置。

低频维度反而没问题，它本来就转得慢。

> 关键：**外推失效主要发生在高频维度，不是所有维度。**

### 3. 三种方法对比

| 方法 | 做法 | 直觉 |
|------|------|------|
| **Position Interpolation** | 所有位置线性压缩：`pos → pos × (L_train / L_test)` | 简单粗暴，长距离分辨率全丢 |
| **NTK-aware** | 调大 `base`，高频少动、低频多动 | 只压必要的部分 |
| **YaRN** | NTK-by-parts：按维度分段处理 + attention scaling | 兼顾两端 |

### 4. NTK-aware 在做什么

`base` 变大 → 所有 `θ_i` 变小 → 旋转变慢 → 同一段位置范围内"转过的圈数"变少。

因为 `θ_i = pos × base^(-2i/d)`，`base` 增大对**高频维度影响小**（指数因子接近 0），对**低频维度影响大**。这正好对应上面说的"只需要压低频、保住高频"。

### 5. YaRN 更进一步

YaRN 认为不该一刀切，而是：

1. **NTK-by-parts** —— 高频维度完全不插值（保住近距离精度），低频维度完全插值，中间的平滑过渡
2. **Attention scaling** —— 上下文变长后 attention 分布会更"平"（熵增），用温度系数补偿

效果：相比纯 PI，YaRN 只需 0.1% 的训练量就能扩到 128K。

### 6. 一个容易忽略的前提

**位置编码不是长上下文的全部。** 即使位置编码完美外推，模型仍需要：

- 训练时见过足够长的**依赖关系**（否则学不会用远距离信息）
- Attention 本身能聚焦到远处（否则"看见"也没用）

这就是为什么工业界普遍做法是：**改 RoPE + 少量长文本继续训练**，而不是纯靠改配置。

## 面试延伸

- **为什么 Llama 系列 base 从 10000 调到 500000？** 就是 NTK-aware 思路的工程落地
- **PI 为什么比 NTK 差？** 它把所有维度一起压缩，高频维度的分辨率被无谓牺牲掉了
- **怎么验证外推是否真的有效？** 别只看 perplexity，要测 needle-in-a-haystack 这类长距离检索任务

## 延伸阅读

- RoPE 原论文：*RoFormer: Enhanced Transformer with Rotary Position Embedding*
- YaRN 论文：*YaRN: Efficient Context Window Extension of Large Language Models* (ICLR 2024)
- 博客：*Extending Context Window of LLMs* (EleutherAI)
