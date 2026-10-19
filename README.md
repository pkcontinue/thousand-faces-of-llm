<div align="center">

# 🎭 千面 LLM

**每日一题 · 累计千问**

把大模型知识拆成一千个切面 —— 从一个问题出发，理解一整块系统。

[![Questions](https://img.shields.io/badge/进度-2%20%2F%201000-7AA2F7?style=flat-square&labelColor=1a1b26)](./questions/)
[![License](https://img.shields.io/badge/License-MIT-9ECE6A?style=flat-square&labelColor=1a1b26)](./LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-BB9AF7?style=flat-square&labelColor=1a1b26)](./CONTRIBUTING.md)

</div>

---

## 这是什么

一个大模型知识库，按 **每天一个问题** 的节奏累积。

每个问题都长这样：先用一句话回答，再把背后的系统讲清楚 —— 公式、权衡、工程坑、延伸阅读。目标不是背答案，而是**通过一个问题吃透一整条链路**。

计划覆盖模型架构、推理优化、Agent、RAG、训练对齐与评测。目前已收录 **2 篇**专题，编号保留原有顺序。

## 从这里开始

- 对检索与 Agent 感兴趣：先读 [DeepSearch 中的 Query 筛选与难题构造](./questions/006-deepsearch-query-filtering.md)。
- 对后训练与强化学习感兴趣：先读 [Mixed RL 数据合版与 MOPD 合版](./questions/007-mixed-rl-data-merging-mopd.md)。
- 想贡献新内容：从 [问题模板](./templates/question.md) 和 [贡献指南](./CONTRIBUTING.md) 开始。

## 目录

<!-- INDEX:START -->
| # | 问题 | 分类 |
|---|------|------|
| 006 | [DeepSearch 中的 Query 筛选与难题构造](./questions/006-deepsearch-query-filtering.md) | 推理优化 |
| 007 | [Mixed RL 数据合版与 MOPD 合版](./questions/007-mixed-rl-data-merging-mopd.md) | 训练对齐 |
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
# 将 NNN 替换为未使用的三位编号，将 your-slug 替换为英文短名
cp templates/question.md questions/NNN-your-slug.md
# 编辑新文件的 frontmatter 和正文
# 手动更新 README 目录、已收录篇数和顶部进度徽章
```

---

<div align="center">
<sub>一天一个问题，一年后你会感谢现在的自己。</sub>
</div>
