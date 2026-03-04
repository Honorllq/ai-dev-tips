---
title: "DeepSeek 完全指南：从入门到实战（2026 年最新）"
date: 2026-02-26
draft: false
tags: ["DeepSeek", "大模型", "AI工具", "API"]
categories: ["AI工具教程"]
summary: "DeepSeek 以不到 600 万美元的训练成本震惊全球 AI 行业。本文全面介绍 DeepSeek 的模型家族、API 使用、本地部署、与 GPT/Claude 的对比，帮你快速上手这个性价比之王。"
---

2025 年 1 月 27 日，被称为"AI 行业的黑色星期一"。DeepSeek-R1 的发布直接导致英伟达单日市值蒸发 **5890 亿美元**，创下美国公司历史上最大单日市值跌幅。

一个来自中国的 AI 公司，用不到 **600 万美元**的训练成本，做出了媲美 OpenAI o1 的推理模型——这件事彻底改写了 AI 行业的竞争格局。

## DeepSeek 是什么

DeepSeek（深度求索）是一家总部位于杭州的 AI 研究公司，2023 年由梁文锋创立。

### 关键信息

| 维度 | 详情 |
|------|------|
| **创始人** | 梁文锋（1985 年生，浙江大学电子信息工程硕士） |
| **背景** | 量化对冲基金"幻方量化"联合创始人，管理资产曾超千亿 |
| **团队** | 不到 200 人 |
| **融资** | 零外部融资，全部由幻方量化自有资金支持 |
| **算力** | 约 50,000 张 NVIDIA GPU（H800/H100/H20） |
| **论文** | DeepSeek-R1 于 2025 年 9 月发表在 Nature |

梁文锋持有公司 **84.29%** 的股份和近 100% 的投票权。这意味着 DeepSeek 不受资本绑架，可以完全按自己的节奏做研究。

## DeepSeek 模型家族

DeepSeek 在短短两年内发布了大量模型，以下是核心模型线。

### 主力模型

| 模型 | 发布时间 | 参数量 | 特点 |
|------|---------|--------|------|
| **DeepSeek-V3** | 2024.12 | 671B（激活 37B） | MoE 架构，训练仅 $5.6M |
| **DeepSeek-R1** | 2025.1 | 671B（激活 37B） | 推理模型，匹配 OpenAI o1 |
| **DeepSeek-V3.1** | 2025.8 | 685B（激活 37B） | 混合思考模型 |
| **DeepSeek-V3.2** | 2025.12 | 671B（激活 37B） | 工具调用 + 思考集成 |
| **DeepSeek-V4**（预告） | 2026.3 | 未公布 | 原生多模态 |

### R1 蒸馏模型（开源 MIT 协议）

DeepSeek 将 R1 的推理能力"蒸馏"到更小的模型中，全部以 MIT 协议开源：

| 模型 | 基础模型 | 参数量 | 亮点 |
|------|---------|--------|------|
| R1-Distill-Qwen-1.5B | Qwen2.5-1.5B | 1.5B | 可在手机运行，数学超 GPT-4o |
| R1-Distill-Qwen-7B | Qwen2.5-7B | 7B | 速度与质量的平衡 |
| R1-Distill-Llama-8B | Llama-3.1-8B | 8B | 兼容 Llama 生态 |
| R1-Distill-Qwen-14B | Qwen2.5-14B | 14B | 甜蜜点 |
| **R1-Distill-Qwen-32B** | Qwen2.5-32B | 32B | **超越 OpenAI o1-mini** |
| R1-Distill-Llama-70B | Llama-3.3-70B | 70B | 最接近完整 R1 |

### 核心技术创新

DeepSeek 的成功不只是"便宜"，而是一系列架构创新：

1. **Multi-head Latent Attention (MLA)**：压缩 KV 缓存，内存减少 60%+
2. **无辅助损失的 MoE 负载均衡**：消除传统 MoE 的质量损耗
3. **FP8 混合精度训练**：首个端到端 FP8 训练的大模型，内存带宽减半
4. **多 Token 预测 (MTP)**：同时预测多个未来 token
5. **GRPO 算法**：R1 的强化学习推理训练核心

## DeepSeek vs GPT vs Claude 基准测试

### DeepSeek-R1 vs OpenAI o1

| 测试 | DeepSeek-R1 | OpenAI o1 | OpenAI o1-mini |
|------|------------|-----------|----------------|
| AIME 2024（数学竞赛） | **79.8%** | 79.2% | 63.6% |
| MATH-500 | **97.3%** | 96.4% | 90.0% |
| SWE-bench（编程） | **49.2%** | 48.9% | - |
| MMLU（综合知识） | 90.8% | **91.8%** | 85.2% |
| GPQA Diamond | 71.5% | **75.7%** | 60.0% |

### DeepSeek-V3 vs GPT-4o vs Claude 3.5

| 测试 | DeepSeek-V3 | GPT-4o | Claude 3.5 Sonnet |
|------|------------|--------|-------------------|
| MMLU | **88.5%** | 87.2% | 88.3% |
| HumanEval（编程） | **82.6%** | 80.5% | - |
| MATH-500 | **90.2%** | 74.6% | - |

**结论**：DeepSeek 在数学、编程方面与顶级闭源模型打平甚至超越，价格却只有对方的 **5%**。

## API 使用指南

### 注册与获取 API Key

1. 访问 [platform.deepseek.com](https://platform.deepseek.com/)
2. 注册账号
3. 进入"API Keys"页面，创建密钥
4. 充值余额（支持支付宝）

### API 定价

DeepSeek 的定价远低于竞争对手：

| 模型 | 输入（每百万 token） | 输出（每百万 token） |
|------|--------------------|--------------------|
| **DeepSeek（缓存命中）** | **$0.028** | $0.42 |
| **DeepSeek（缓存未命中）** | **$0.28** | $0.42 |
| GPT-4o | $5.00 | $15.00 |
| Claude Opus 4.6 | $5.00 | $25.00 |

**价格对比**：
- DeepSeek 输入价格比 GPT-4o **便宜 18 倍**
- 缓存命中时比 GPT-4o **便宜 178 倍**
- 输出价格比 GPT-4o **便宜 36 倍**

### Python 调用示例

DeepSeek API 兼容 OpenAI 格式，可以直接用 OpenAI SDK：

```python
from openai import OpenAI

client = OpenAI(
    api_key="你的-deepseek-api-key",
    base_url="https://api.deepseek.com"
)

# 普通对话
response = client.chat.completions.create(
    model="deepseek-chat",  # V3.2 非思考模式
    messages=[
        {"role": "system", "content": "你是一个有帮助的AI助手。"},
        {"role": "user", "content": "用Python实现快速排序"}
    ],
    stream=False
)
print(response.choices[0].message.content)
```

```python
# 推理模式（类似 o1 的深度思考）
response = client.chat.completions.create(
    model="deepseek-reasoner",  # V3.2 思考模式
    messages=[
        {"role": "user", "content": "证明√2是无理数"}
    ]
)
print(response.choices[0].message.content)
```

### API 模型名称

| 模型名 | 说明 |
|--------|------|
| `deepseek-chat` | DeepSeek-V3.2（非思考模式） |
| `deepseek-reasoner` | DeepSeek-V3.2（思考模式） |

### API 功能

- JSON 输出模式
- 函数调用（Function Calling）
- 代码补全（FIM，Beta）
- 流式输出
- 128K 上下文长度

## 本地部署（Ollama）

如果你想完全离线使用 DeepSeek，可以通过 Ollama 在本地运行蒸馏版。

### 安装 Ollama

```bash
# macOS
brew install ollama

# Linux
curl -fsSL https://ollama.com/install.sh | sh

# Windows：去 ollama.com 下载安装包
```

### 下载并运行 DeepSeek

```bash
# 7B 版本（推荐 8GB+ 显存）
ollama run deepseek-r1:7b

# 14B 版本（推荐 12GB+ 显存）
ollama run deepseek-r1:14b

# 32B 版本（推荐 24GB+ 显存，超越 o1-mini）
ollama run deepseek-r1:32b
```

### 显存需求参考

| 模型 | 文件大小 | 最低显存 | 推荐显卡 |
|------|---------|---------|---------|
| deepseek-r1:1.5b | ~1GB | 4GB | 任意独显 |
| deepseek-r1:7b | ~4.7GB | 8GB | RTX 3060+ |
| deepseek-r1:14b | ~9GB | 12GB | RTX 4070+ |
| deepseek-r1:32b | ~19GB | 24GB | RTX 3090/4090 |
| deepseek-r1:70b | ~43GB | 48GB | 多卡 A100 |

### 本地 API 调用

Ollama 启动后会在 `localhost:11434` 提供 API：

```python
from openai import OpenAI

client = OpenAI(
    api_key="ollama",  # 任意字符串
    base_url="http://localhost:11434/v1"
)

response = client.chat.completions.create(
    model="deepseek-r1:14b",
    messages=[{"role": "user", "content": "你好"}]
)
print(response.choices[0].message.content)
```

## "DeepSeek 冲击"始末

### 事件时间线

| 时间 | 事件 |
|------|------|
| 2024.12.27 | DeepSeek-V3 发布，$5.6M 训练成本引关注 |
| 2025.1.10 | DeepSeek App 上线 iOS/Android |
| 2025.1.20 | DeepSeek-R1 正式发布 |
| 2025.1.25-26 | App 登顶美国 Apple Store 第一名，超越 ChatGPT |
| **2025.1.27** | **"黑色星期一"：英伟达暴跌 17%，蒸发 $5890 亿** |

### 为什么震动市场

1. **成本颠覆**：$5.6M vs 竞品 $50-100M+，质疑天价 AI 基础设施投资是否必要
2. **性能持平**：开源模型匹配闭源顶级模型
3. **出口管制质疑**：用受限的 H800 GPU 达到同等效果，美国芯片出口管制有效性被质疑
4. **开源威胁**：MIT 协议开源动摇了闭源 AI 公司的定价模式

### $5.6M 训练成本的争议

这个数字被广泛讨论，但多位分析师指出它有误导性：

- **只计算了最终训练轮次的 GPU 小时数**
- 不包含：研发试错成本、数据收集处理、早期实验、基础设施购置、人员薪资
- Scale AI CEO 公开表示 DeepSeek "在真实成本上有所隐瞒"
- 实际总成本远高于 $5.6M

## 注意事项与限制

### 审查问题

DeepSeek 遵循中国法规进行内容审查：
- 拒绝讨论天安门事件、台湾问题、新疆等敏感话题
- 部分政治相关问题会推送中国官方立场
- 用户报告回答有时会被实时编辑/删除

### 数据隐私

- 隐私政策明确声明个人信息存储在**中国境内**
- 意大利是首个封禁 DeepSeek 的国家（2025.1.30）
- 澳大利亚、韩国、台湾禁止在政府设备使用

### 技术限制

- 与所有 LLM 一样存在幻觉问题
- 低于 14B 参数的蒸馏版质量明显下降
- 复杂查询的响应速度有时较慢

## 使用建议

```
使用场景                → 推荐方案
────────────────────────────────────────
日常对话/写作           → DeepSeek API（deepseek-chat）
数学/逻辑推理           → DeepSeek API（deepseek-reasoner）
预算有限的开发者         → DeepSeek API（最便宜的选择）
注重隐私/离线使用       → Ollama + deepseek-r1:14b
高质量编程              → Claude Code 或 DeepSeek + Cursor
敏感话题讨论            → 改用 Claude 或 GPT（无审查）
```

## 总结

DeepSeek 用事实证明了：**顶级 AI 不一定需要天价投入**。它的核心价值在于：

1. **极致性价比**：同等质量，价格只有竞品的 5%
2. **开源生态**：MIT 协议让任何人都能使用和修改
3. **技术创新**：MLA、FP8 训练等架构创新被全行业学习
4. **本地可用**：通过 Ollama 蒸馏版可完全离线运行

2026 年 3 月，DeepSeek-V4 即将发布，这将是首个原生多模态版本。DeepSeek 的故事还远未结束。

### 参考资料

- DeepSeek 官方对话：[chat.deepseek.com](https://chat.deepseek.com/)
- API 平台：[platform.deepseek.com](https://platform.deepseek.com/)
- API 文档：[api-docs.deepseek.com](https://api-docs.deepseek.com/)
- GitHub：[github.com/deepseek-ai](https://github.com/deepseek-ai)
- Ollama：[ollama.com/library/deepseek-r1](https://ollama.com/library/deepseek-r1)

---

*你在用 DeepSeek 吗？欢迎在评论区分享你的使用体验！*
