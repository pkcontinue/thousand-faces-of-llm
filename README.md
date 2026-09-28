<div align="center">

# 🎭 千面 LLM

**每日一题 · 累计千问**

把大模型知识拆成一千个切面 —— 从一个问题出发，理解一整块系统。

[![Questions](https://img.shields.io/badge/进度-5%20%2F%201000-7AA2F7?style=flat-square&labelColor=1a1b26)](./questions)
[![License](https://img.shields.io/badge/License-MIT-9ECE6A?style=flat-square&labelColor=1a1b26)](./LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-BB9AF7?style=flat-square&labelColor=1a1b26)](./CONTRIBUTING.md)

</div>

---

## 这是什么

一个大模型知识库，按 **每天一个问题** 的节奏累积。

每个问题都长这样：先用一句话回答，再把背后的系统讲清楚 —— 公式、权衡、工程坑、延伸阅读。目标不是背答案，而是**通过一个问题吃透一整条链路**。

问题覆盖从模型架构、推理优化，到 Agent、RAG、训练对齐与评测。

## 目录

<!-- INDEX:START -->
| # | 问题 | 分类 |
|---|------|------|
| 001 | [为什么 LLM 推理的显存瓶颈在 KV Cache？](./questions/001-kv-cache.md) | 推理优化 |
| 002 | [RoPE 为什么外推失效？NTK-aware 和 YaRN 在做什么？](./questions/002-position-encoding.md) | 模型架构 |
| 003 | [Agent 跑 50 轮工具调用后上下文爆炸，怎么办？](./questions/003-agent-context.md) | Agent |
| 004 | [SFT 之后为什么还要 RLHF / DPO？DPO 的局限在哪？](./questions/004-dpo-limitation.md) | 训练对齐 |
| 005 | [为什么 LLM Benchmark 分数不可信？](./questions/005-benchmark-trust.md) | 评测 |
<!-- INDEX:END -->

## 分类

| 分类 | 说明 |
|------|------|
| 🚀 **推理优化** | KV Cache、批处理、量化、投机解码、PagedAttention |
| 🏗️ **模型架构** | Attention 变体、位置编码、MoE、归一化 |
| 🤖 **Agent** | 工具调用、多智能体、上下文工程、MCP |
| 🔍 **RAG / 检索** | 切分、向量检索、重排、GraphRAG |
| 🎯 **训练对齐** | SFT、RLHF、DPO、RL 训练、数据配比 |
| 📊 **评测** | Benchmark 可信度、LLM-as-Judge、污染检测 |
| ⚙️ **工程实践** | 服务化、可观测性、成本控制、灰度 |

## 怎么用

- **当复习手册** —— 顺着目录刷，一周一个分类
- **当面试准备** —— 每题都能延伸到系统设计
- **当选题池** —— 每个问题背后都能挖出一个项目

## 参与

欢迎提交你自己的问题。格式见 [`templates/question.md`](./templates/question.md)，流程见 [`CONTRIBUTING.md`](./CONTRIBUTING.md)。

```bash
# 新增一个问题
cp templates/question.md questions/006-your-slug.md
# 编辑 frontmatter 和内容
python scripts/build_index.py   # 自动更新上面的目录
```

---

<div align="center">
<sub>一天一个问题，一年后你会感谢现在的自己。</sub>
</div>
