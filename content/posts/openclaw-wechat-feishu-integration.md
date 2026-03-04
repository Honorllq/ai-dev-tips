---
title: "OpenClaw + 飞书/微信：打造你的 AI 私人助手"
date: 2026-03-07
draft: false
tags: ["OpenClaw", "飞书", "微信", "AI助手", "自动化"]
categories: ["AI工具教程"]
series: ["AI应用实战系列"]
keywords: ["OpenClaw教程", "飞书机器人", "微信AI助手", "AI自动化", "飞书AI"]
summary: "用 OpenClaw 连接飞书和微信，搭建一个 7×24 小时在线的 AI 私人助手。自动回复消息、总结文档、管理日程——全部本地部署，数据不出你的服务器。"
---

想象一下：在飞书群里 @你的 AI 助手，它自动帮你总结会议纪要；在微信里发一张图片，它自动识别内容并给出建议；你离开电脑后，它继续帮你处理消息和任务。

这就是 **OpenClaw + 飞书/微信** 能做到的事情。

## 什么是 OpenClaw

OpenClaw 是一个开源的 AI 管家框架，可以连接多个 AI 模型和消息平台。它的核心特点：

- **多模型支持**：同时连接 OpenAI、Claude、DeepSeek、本地 Ollama
- **多平台接入**：飞书、微信、Telegram、Discord、Slack
- **插件系统**：可扩展的工具和能力
- **本地部署**：数据完全在你的服务器上
- **完全开源**：MIT 协议

## 架构概览

```
┌──────────┐     ┌──────────┐     ┌──────────┐
│   飞书    │     │   微信    │     │ Telegram │
│  机器人   │     │   Bot    │     │   Bot    │
└────┬─────┘     └────┬─────┘     └────┬─────┘
     │                │                │
     └────────────────┼────────────────┘
                      │
              ┌───────▼───────┐
              │   OpenClaw    │
              │   核心引擎     │
              │  （消息路由）   │
              └───────┬───────┘
                      │
        ┌─────────────┼─────────────┐
        │             │             │
  ┌─────▼────┐  ┌─────▼────┐  ┌────▼─────┐
  │  Claude   │  │ DeepSeek │  │  Ollama  │
  │  API      │  │  API     │  │  本地    │
  └──────────┘  └──────────┘  └──────────┘
```

## 准备工作

### 服务器要求

| 配置 | 最低要求 | 推荐配置 |
|------|---------|---------|
| CPU | 2 核 | 4 核 |
| 内存 | 2GB | 4GB |
| 存储 | 10GB | 20GB |
| 系统 | Ubuntu 20.04+ | Ubuntu 22.04 |
| 网络 | 需要公网 IP 或内网穿透 | 有公网 IP |

如果要跑本地模型（Ollama），需要额外的 GPU 资源。

### 需要准备的账号

| 平台 | 需要 | 说明 |
|------|------|------|
| 飞书开放平台 | 企业版 | 创建自建应用 |
| 微信公众平台 | 订阅号/服务号 | 或使用个人微信方案 |
| AI API | 至少一个 | OpenAI / Claude / DeepSeek |

## 安装 OpenClaw

### Docker 方式（推荐）

```bash
# 克隆项目
git clone https://github.com/anthropics/openclaw.git
cd openclaw

# 复制配置文件
cp .env.example .env

# 编辑配置
nano .env
```

`.env` 文件关键配置：

```bash
# AI 模型配置（至少配一个）
OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
DEEPSEEK_API_KEY=sk-...

# 默认模型
DEFAULT_MODEL=claude-sonnet-4-5

# 飞书配置
FEISHU_APP_ID=cli_xxx
FEISHU_APP_SECRET=xxx
FEISHU_VERIFICATION_TOKEN=xxx
FEISHU_ENCRYPT_KEY=xxx

# 服务配置
PORT=3000
WEBHOOK_URL=https://your-domain.com/webhook
```

启动服务：

```bash
docker compose up -d
```

### 手动安装

```bash
# 安装 Node.js 18+
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# 安装依赖
cd openclaw
npm install

# 构建
npm run build

# 启动
npm start
```

## 连接飞书

### 第一步：创建飞书应用

1. 打开 [飞书开放平台](https://open.feishu.cn/)
2. 进入「开发者后台」→「创建企业自建应用」
3. 填写应用名称（如「AI 助手」）和描述
4. 记录 `App ID` 和 `App Secret`

### 第二步：配置权限

在应用的「权限管理」中，添加以下权限：

```
im:message              # 获取消息
im:message:send         # 发送消息
im:chat                 # 获取群信息
im:resource             # 获取文件资源
contact:user.base       # 获取用户基本信息
```

### 第三步：配置事件订阅

1. 进入「事件订阅」
2. 设置请求地址：`https://your-domain.com/webhook/feishu`
3. 添加事件：
   - `im.message.receive_v1`（接收消息）
   - `im.chat.member.bot.added_v1`（机器人被拉入群）

### 第四步：配置 OpenClaw

```json
// config/feishu.json
{
  "platform": "feishu",
  "appId": "你的 App ID",
  "appSecret": "你的 App Secret",
  "verificationToken": "你的 Verification Token",
  "encryptKey": "你的 Encrypt Key",
  "botName": "AI助手",
  "triggerMode": "mention",
  "defaultModel": "claude-sonnet-4-5",
  "maxContextMessages": 20,
  "systemPrompt": "你是一个智能助手，帮助团队成员回答问题、总结文档、翻译内容。回答要简洁专业。"
}
```

### 第五步：发布应用

1. 在飞书开放平台提交审核
2. 审核通过后，在「版本管理」中发布
3. 在飞书中搜索你的应用名称，添加到群聊

### 使用效果

在飞书群里：
```
用户：@AI助手 帮我总结一下今天讨论的三个议题

AI助手：根据今天的讨论，三个议题总结如下：

1. **Q2 产品规划**：确定了三个核心功能方向...
2. **技术架构升级**：计划在 5 月完成微服务迁移...
3. **团队招聘**：新增 2 个前端和 1 个后端名额...

需要我展开哪个议题的细节？
```

## 连接微信

微信的接入有两种方案：

### 方案 A：微信公众号（推荐）

适合面向外部用户的场景。

**步骤**：

1. 注册微信公众号（订阅号或服务号）
2. 进入「开发」→「基本配置」
3. 配置服务器地址：`https://your-domain.com/webhook/wechat`
4. 获取 `AppID`、`AppSecret`、`Token`、`EncodingAESKey`

OpenClaw 配置：

```json
// config/wechat.json
{
  "platform": "wechat_official",
  "appId": "你的 AppID",
  "appSecret": "你的 AppSecret",
  "token": "你的 Token",
  "encodingAESKey": "你的 EncodingAESKey",
  "defaultModel": "deepseek-chat",
  "systemPrompt": "你是一个有帮助的 AI 助手。请用简洁的中文回答。"
}
```

### 方案 B：个人微信接入

通过第三方框架（如 wechaty）实现个人微信的 AI 自动回复。

**注意**：这种方式有被微信封号的风险，仅建议用于测试和个人用途。

```bash
# 安装 wechaty 插件
npm install openclaw-plugin-wechaty

# 配置
{
  "platform": "wechaty",
  "puppet": "wechaty-puppet-wechat4u",
  "autoReply": true,
  "privateChat": true,
  "groupChat": true,
  "groupTrigger": "@AI"
}
```

## 高级配置

### 多模型路由

根据不同的场景自动选择最合适的模型：

```json
{
  "modelRouting": {
    "rules": [
      {
        "condition": "message contains '翻译' or 'translate'",
        "model": "deepseek-chat",
        "reason": "翻译任务用 DeepSeek 性价比最高"
      },
      {
        "condition": "message contains '代码' or '编程'",
        "model": "claude-sonnet-4-5",
        "reason": "编程任务用 Claude 质量最好"
      },
      {
        "condition": "message length > 2000",
        "model": "deepseek-chat",
        "reason": "长文本分析用 DeepSeek 节省成本"
      },
      {
        "condition": "default",
        "model": "claude-haiku-4-5",
        "reason": "日常对话用 Haiku 速度快且便宜"
      }
    ]
  }
}
```

### 知识库集成（RAG）

让 AI 助手能够查询你的内部文档：

```json
{
  "rag": {
    "enabled": true,
    "vectorStore": "chroma",
    "embeddingModel": "nomic-embed-text",
    "documents": [
      "./docs/company-wiki/",
      "./docs/product-manual/"
    ],
    "chunkSize": 500,
    "topK": 5
  }
}
```

上传文档到知识库：

```bash
# 上传单个文件
openclaw rag upload ./docs/handbook.pdf

# 批量上传目录
openclaw rag upload ./docs/wiki/ --recursive

# 查看知识库状态
openclaw rag status
```

### 定时任务

让 AI 助手自动执行定期任务：

```json
{
  "scheduledTasks": [
    {
      "name": "daily-summary",
      "cron": "0 18 * * 1-5",
      "action": "summarize_channel",
      "channel": "feishu:chat_id_xxx",
      "prompt": "总结今天群里的重要讨论和待办事项",
      "notifyTo": "feishu:user_id_xxx"
    },
    {
      "name": "morning-briefing",
      "cron": "0 9 * * 1-5",
      "action": "custom_prompt",
      "prompt": "查看今天的天气和新闻摘要",
      "notifyTo": "wechat:user_xxx"
    }
  ]
}
```

### 插件系统

OpenClaw 支持通过插件扩展功能：

```javascript
// plugins/weather.js
module.exports = {
  name: "weather",
  description: "查询天气信息",
  triggers: ["天气", "weather", "几度"],
  async handler(message, context) {
    const city = extractCity(message.text);
    const weather = await fetchWeather(city);
    return `${city}今天${weather.desc}，温度${weather.temp}°C`;
  }
};
```

安装插件：

```bash
openclaw plugin install ./plugins/weather.js
openclaw plugin list
```

## 成本估算

### API 费用

以一个 50 人团队、每天约 500 条消息为例：

| 模型 | 单次平均成本 | 日成本 | 月成本 |
|------|------------|--------|--------|
| Claude Haiku 4.5 | ~$0.001 | ~$0.5 | ~$15 |
| DeepSeek Chat | ~$0.0003 | ~$0.15 | ~$4.5 |
| Claude Sonnet 4.5 | ~$0.005 | ~$2.5 | ~$75 |

**建议**：日常对话用 Haiku 或 DeepSeek，复杂问题自动路由到 Sonnet。月均成本可控制在 **$20-50** 之间。

### 服务器费用

| 方案 | 月费 | 说明 |
|------|------|------|
| 国内云服务器（2C4G） | ¥50-100 | 阿里云/腾讯云轻量应用服务器 |
| 海外 VPS（2C4G） | $5-10 | Vultr, DigitalOcean |
| 树莓派（家用） | ¥0 | 电费忽略不计，需要内网穿透 |

## 安全注意事项

### 数据安全

1. **HTTPS 必须**：所有 webhook 地址都要使用 HTTPS
2. **API Key 保护**：使用环境变量存储，不要硬编码在配置文件中
3. **消息加密**：飞书支持消息加密，务必开启 `encryptKey`
4. **访问控制**：限制哪些群/用户可以使用 AI 助手

### 内容安全

```json
{
  "contentFilter": {
    "enabled": true,
    "blockPatterns": [
      "密码", "token", "secret",
      "银行卡", "身份证"
    ],
    "warningMessage": "检测到敏感信息，已自动过滤。请不要在对话中发送密码、密钥等敏感数据。"
  }
}
```

### 速率限制

防止滥用：

```json
{
  "rateLimit": {
    "perUser": {
      "maxRequests": 50,
      "windowMinutes": 60
    },
    "perGroup": {
      "maxRequests": 200,
      "windowMinutes": 60
    }
  }
}
```

## 常见问题

### Q1：飞书机器人不回复消息

检查清单：
1. 确认 webhook URL 可以从公网访问
2. 检查事件订阅是否配置正确
3. 查看 OpenClaw 日志：`docker compose logs -f`
4. 确认机器人已被添加到群聊中

### Q2：微信公众号回复超时

微信要求 5 秒内返回响应。解决方案：
- 使用客服消息接口（异步回复）
- 先回复"思考中..."，然后异步发送完整回复
- 使用速度更快的模型（Haiku 或 DeepSeek）

### Q3：如何降低 API 成本

1. 使用模型路由，简单问题用便宜模型
2. 设置用户每日请求上限
3. 开启上下文缓存（减少重复的系统提示开销）
4. 用 DeepSeek 替代 GPT-4 处理非关键任务

### Q4：可以同时连接飞书和微信吗

可以。OpenClaw 支持同时运行多个平台连接器，消息会通过统一的路由引擎处理。

## 总结

OpenClaw + 飞书/微信的组合让你拥有一个 7×24 小时在线的 AI 助手：

```
飞书场景：
- 群内 @AI 回答问题
- 自动总结会议讨论
- 查询内部知识库
- 翻译和文档处理

微信场景：
- 自动回复客户咨询
- 个人 AI 助手（提醒、查询、翻译）
- 内容创作辅助

核心优势：
- 数据完全私有（自部署）
- 多模型灵活切换
- 成本可控（$20-50/月）
- 无限可扩展
```

### 参考链接

- OpenClaw GitHub：[github.com/anthropics/openclaw](https://github.com/anthropics/openclaw)
- 飞书开放平台：[open.feishu.cn](https://open.feishu.cn/)
- 微信公众平台：[mp.weixin.qq.com](https://mp.weixin.qq.com/)

---

## 延伸阅读

- [OpenClaw 本地部署完整教程](/posts/openclaw-local-deploy/) — 从零开始部署 OpenClaw
- [Ollama 完全指南](/posts/ollama-local-llm-guide/) — 用 Ollama 作为 OpenClaw 的本地模型后端
- [MCP 协议详解](/posts/mcp-protocol-guide/) — OpenClaw 使用的工具连接标准
- [AI Agent 赚钱变现](/posts/ai-agent-monetization/) — 把 AI 助手做成服务来变现

---

*你想用 AI 助手做什么？欢迎在评论区分享你的想法！*
