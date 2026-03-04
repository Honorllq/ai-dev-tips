---
title: "Vibe Coding 完全指南：让 AI 替你写代码的新方式"
date: 2026-03-01T08:00:00+08:00
draft: false
tags: ["Vibe Coding", "AI编程", "Cursor", "Claude Code"]
categories: ["AI工具教程"]
summary: "Vibe Coding（氛围编程）是 Andrej Karpathy 提出的新概念：不看代码，不读 diff，全凭感觉让 AI 写。它已成为 2025 年 Collins 词典年度词汇。本文解析这个现象、工具、争议，以及如何正确使用。"
---

2025 年 2 月 2 日，OpenAI 联合创始人 Andrej Karpathy 在 X 上发了一条推文，获得了超过 450 万次浏览：

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."

到 2025 年 11 月，Collins 词典将 **Vibe Coding** 选为年度词汇。一年后，Karpathy 本人宣布 Vibe Coding 已经"过时"了，取而代之的是"Agentic Engineering"。

这篇文章带你了解 Vibe Coding 的前世今生。

## 什么是 Vibe Coding

**Vibe Coding（氛围编程）** 的核心是：用自然语言告诉 AI 你想要什么，AI 生成代码，你不读代码，只看它能不能跑。

Karpathy 的原始描述：
- "Accept All"——永远全部接受，不看 diff
- 报错了就把错误信息粘贴回去，通常就能修好
- 代码量超出了自己的理解范围
- AI 修不了的 Bug 就绕过去，或者随便改改直到消失
- "不算真正的编程——我就是看看、说说、跑跑、粘粘贴贴"

**关键区分**（Django 联合创始人 Simon Willison）：
> "如果 AI 写了你所有的代码，但你审查、测试、理解了每一行——那不是 Vibe Coding，那是用 AI 当打字助手。"

Vibe Coding 的关键在于**不审查代码**。

## 为什么火了

### 数据说话

- **85%** 的开发者已在使用 AI 编程工具
- **41%** 的新代码由 AI 生成
- **Y Combinator W25 批次 25%** 的创业公司代码库有 95% 由 AI 生成
- Lovable（Vibe Coding 平台）估值 **66 亿美元**，8 个月达到 2 亿美元 ARR
- Cursor 年收入突破 **20 亿美元**

### 谁在 Vibe Coding

不只是程序员。75% 的 Replit 用户从未写过一行代码。创业者、设计师、教练、研究人员都在用 AI 构建应用。

## Vibe Coding 工具

### IDE 类

| 工具 | 特点 | 价格 |
|------|------|------|
| **Cursor** | 最主流的 AI IDE | $20/月起 |
| **Claude Code** | 终端 Agent，推理最强 | $20/月起 |
| **Windsurf** | 免费 IDE，上手最快 | 免费 |
| **GitHub Copilot** | 最普及，IDE 支持最广 | $10/月起 |

### 无代码/低代码平台

| 工具 | 特点 | 价格 |
|------|------|------|
| **Lovable** | 自然语言到全栈应用 | 免费起 |
| **Bolt.new** | 浏览器内 AI IDE | 免费起 |
| **Replit** | 云端一站式开发 | 免费起 |
| **v0.dev** | 前端 UI 生成（React/Next.js） | 免费起 |

**常见工作流**："Lovable 快速出原型 → Cursor 接手做正式版"

## Vibe Coding 的实际案例

- **Refetch**：Hacker News 替代品，15 小时构建，边看 Netflix 边写
- **CareerCloud**：AI 简历优化器
- **CarbScan**：糖尿病血糖管理工具
- 一位非技术背景的教练用 AI 构建了心理学生产力应用
- 独立开发者 8 个月用 AI 做了 28 个 App，月收入从 $200 涨到 $10,000+

## 争议：好还是坏？

### 支持方

1. **降低门槛**：非程序员也能做软件
2. **速度飞快**：几小时出原型，传统开发可能需要几周
3. **释放创造力**：想到什么就能做出来试试
4. **经济效益**：独立开发者不需要开发团队也能做产品

### 反对方

1. **安全漏洞**：AI 生成代码的安全缺陷是人工代码的 **2 倍**（IBM 研究）
2. **技术债务**：以 AI 的速度疯狂堆积技术债
3. **调试更难**：45% 的开发者表示调试 AI 代码比自己写的更费时
4. **虚假生产力**：METR 研究发现有经验的开发者用 AI 工具反而**慢了 19%**（尽管他们自己认为快了 20%）
5. **40%** 的初级开发者承认部署了自己不完全理解的 AI 代码

### 行业态度

**Mark Zuckerberg**：每个工程师会变成"技术负责人"，各自带领一支 AI Agent 团队。

**Andrew Ng**（DeepLearning.AI 创始人）：Vibe Coding 是一个"糟糕的名字"，引导 AI 写有用的软件是"深度智力劳动"。

**中文社区反应**：
- 极客公园：Vibe Coding 制造了一种"快乐但无知"的幻觉
- InfoQ：CTO 们集体炮轰 AI 编程——不是失业，而是**失控**
- V2EX：氛围编程的本质是以 AI 的速度疯狂堆积技术债

## Vibe Coding vs 传统编程

| 维度 | Vibe Coding | 传统编程 |
|------|------------|---------|
| 代码来源 | AI 生成 | 手动编写 |
| 代码审查 | 不审查 | 严格审查 |
| 理解程度 | 可能不理解 | 完全理解 |
| 速度 | 极快 | 较慢 |
| 安全性 | 缺陷更多 | 标准安全实践 |
| 适用场景 | 原型、MVP、个人工具 | 生产系统、企业级 |
| 可维护性 | 技术债快速积累 | 长期可维护 |

## 10 条 Vibe Coding 最佳实践

### 规划先行

1. **先写需求文档**：不要上来就让 AI 写，先明确要什么
2. **分步构建**：把任务拆成小模块，不要一次让 AI 写整个应用
3. **先设计数据库**：数据模型确定后再开始

### 过程控制

4. **用 Git 勤提交**：每次 AI 改动后提交，方便回滚
5. **每改一步就测试**：不要堆叠多个改动
6. **设置预算限制**：防止 API 调用费用失控

### 质量保障

7. **给 AI 设置全局规则**：项目级的代码风格和约束
8. **不同任务用不同模型**：简单任务用快模型，复杂任务用强模型
9. **请有经验的人 review**：发布前让真正的开发者看一眼

### 心态

10. **保持低风险**：Vibe Coding 适合周末项目和原型，不适合银行系统

## 从 Vibe Coding 到 Agentic Engineering

2026 年 2 月，Karpathy 在 Vibe Coding 一周年时宣布：

> "Vibe Coding 已经过时了。我更喜欢 **Agentic Engineering**——99% 的时间你不直接写代码，而是编排 Agent 来写，自己做监督。"

这标志着 AI 辅助编程的进化：
- **2025 年初**：Vibe Coding——让 AI 写，不看代码
- **2025 年中**：AI-Assisted Engineering——AI 辅助，人审查
- **2026 年**：Agentic Engineering——编排 Agent 团队，人做技术负责人

## 总结

Vibe Coding 不是软件工程的未来，但它是 AI 编程革命的起点。正确的态度是：

- **原型和探索**：放心 Vibe
- **生产系统**：必须审查、测试、理解每一行代码
- **持续学习**：不论 AI 多强，理解代码的能力永远有价值

### 参考资料

- [Karpathy 原始推文](https://x.com/karpathy/status/1886192184808149383)
- [Simon Willison: Not All AI-Assisted Programming Is Vibe Coding](https://simonwillison.net/2025/Mar/19/vibe-coding/)
- [Collins Dictionary Word of the Year 2025](https://www.collinsdictionary.com/woty)
- [METR Study: AI Developer Productivity](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/)

---

*你 Vibe 过吗？欢迎在评论区分享你的 Vibe Coding 体验！*
