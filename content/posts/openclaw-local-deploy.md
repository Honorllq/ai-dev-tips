---
title: "OpenClaw 本地部署完整教程：从零搭建你的私人 AI 助手"
date: 2026-03-04T10:00:00+08:00
draft: false
tags: ["OpenClaw", "AI工具", "本地部署", "Ollama"]
categories: ["AI工具教程"]
summary: "OpenClaw 是 2026 年最火的开源 AI 助手项目（GitHub 250K+ Stars）。本文手把手教你在 Windows/macOS/Linux 上本地部署，接入 DeepSeek 或 Ollama 本地模型，打造 7×24 小时私人 AI 助手。"
---

## OpenClaw 是什么

[OpenClaw](https://github.com/openclaw/openclaw) 是一个开源的本地优先 AI 代理与自动化平台，2026 年 1 月底发布后迅速爆火，GitHub Star 数突破 **25 万**，超越 React 成为 GitHub 上 Star 数最多的软件项目之一。

它的核心特点：

- **本地运行**：数据完全在你自己的设备上，隐私有保障
- **多平台接入**：支持 WhatsApp、Telegram、微信、飞书、钉钉、Discord、Slack 等 10+ 消息平台
- **模型灵活**：可接入 DeepSeek、Claude、GPT、Ollama 本地模型等任意 AI 模型
- **Skills 生态**：1700+ 社区技能，覆盖文件管理、邮件、新闻、GitHub 等各种场景
- **完全免费**：开源项目，只需承担 AI 模型的 API 调用费用

简单来说，OpenClaw 就是一个 **跑在你自己电脑上的 AI 管家**，你可以通过微信、Telegram 等聊天工具随时给它下指令。

## 本地部署 vs 云部署，选哪个？

| 维度 | 本地部署 | 云部署 |
|------|---------|--------|
| **成本** | 0 元（用自己电脑） | 50-100 元/月起 |
| **数据安全** | 数据完全在本地 | 数据在云服务器上 |
| **24 小时在线** | 电脑关机就断 | 7×24 在线 |
| **上手难度** | 3 条命令搞定 | 需要服务器运维基础 |

**建议**：个人使用先本地部署，玩熟了再考虑上云。

## 部署前准备

### 硬件要求

如果只接入云端 API（如 DeepSeek），普通电脑就够了。如果要跑本地模型（Ollama），建议：

- **GPU**：NVIDIA 显存 >= 8GB（RTX 4060 及以上）
- **内存**：>= 16GB
- **磁盘**：至少 20GB 剩余空间

### 安装 Node.js（必需）

OpenClaw 基于 Node.js 运行，需要 **Node.js >= 22**。

**Windows**：去 [nodejs.org](https://nodejs.org/zh-cn/download) 下载安装包，安装时勾选 **"Add Node.js to PATH"**。

**macOS**：

```bash
brew install node
```

**Linux（Ubuntu/Debian）**：

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

验证安装：

```bash
node --version    # 应输出 v22.x.x 或更高
```

## 方式一：官方脚本安装（推荐）

这是最简单的方式，4 步搞定。

### 第 1 步：安装 OpenClaw

**macOS / Linux**：

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**Windows（PowerShell）**：

```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

> 国内网络慢？可以用社区安装脚本：
> ```bash
> curl -fsSL https://raw.githubusercontent.com/miaoxworld/OpenClawInstaller/main/install.sh | bash
> ```

### 第 2 步：运行配置向导

```bash
openclaw onboard --install-daemon
```

这条命令会引导你完成：
1. GitHub 账号认证
2. AI 模型选择和 API Key 配置
3. Gateway 守护进程安装（开机自启）

### 第 3 步：检查网关状态

```bash
openclaw gateway status
```

如果显示正在运行，说明一切正常。没启动的话手动运行：

```bash
openclaw gateway --port 18789
```

### 第 4 步：打开控制面板

```bash
openclaw dashboard
```

浏览器会自动打开 `http://127.0.0.1:18789/`，这就是 OpenClaw 的 Dashboard。

**到这里，你的本地 AI 助手就跑起来了。**

## 方式二：npm 手动安装

如果你更熟悉 npm：

```bash
# 直接安装
npm install -g openclaw

# 国内网络慢先切镜像
npm config set registry https://registry.npmmirror.com
npm install -g openclaw
```

安装后同样执行 `openclaw onboard --install-daemon` 进行配置。

## 方式三：Docker 部署

适合有 Docker 经验的用户，环境隔离更干净：

```bash
# 拉取社区中文镜像
docker pull justlikemaki/openclaw-docker-cn-im:latest

# 下载配置文件
wget https://raw.githubusercontent.com/justlovemaki/OpenClaw-Docker-CN-IM/main/docker-compose.yml
wget https://raw.githubusercontent.com/justlovemaki/OpenClaw-Docker-CN-IM/main/.env.example

# 复制并编辑环境变量（至少配置 API Key）
cp .env.example .env
nano .env

# 启动
docker compose up -d
```

这个社区镜像已经做了中文汉化，还预装了飞书、微信等 IM 插件。

## 配置 AI 模型

OpenClaw 装好后只是一个"壳"，需要接入 AI 模型才能工作。

### 方案一：接入 DeepSeek（推荐新手，性价比最高）

编辑 `~/.openclaw/openclaw.json`：

```json
{
  "models": {
    "providers": {
      "deepseek": {
        "baseUrl": "https://api.deepseek.com/v1",
        "apiKey": "你的 DeepSeek API Key",
        "api": "openai-completions",
        "models": [
          {
            "id": "deepseek-chat",
            "name": "DeepSeek V3",
            "reasoning": false
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "deepseek/deepseek-chat"
      }
    }
  }
}
```

DeepSeek API Key 在 [platform.deepseek.com](https://platform.deepseek.com/) 注册获取，价格非常便宜。

### 方案二：接入 Ollama 本地模型（完全离线）

如果你对隐私要求极高，可以用 Ollama 在本地跑模型。

**安装 Ollama**：

- Windows：去 [ollama.com](https://ollama.com) 下载安装包
- macOS/Linux：`curl -fsSL https://ollama.com/install.sh | sh`

**下载模型**：

```bash
ollama pull qwen2.5:7b    # 约 4.7GB
```

**创建大上下文模型**（OpenClaw 要求 >= 16K context）：

```bash
# 创建 Modelfile
echo 'FROM qwen2.5:7b
PARAMETER num_ctx 32768' > Modelfile

# 创建新模型
ollama create qwen2.5:7b-32k -f Modelfile
```

**配置 OpenClaw 连接 Ollama**：

```json
{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://127.0.0.1:11434/v1",
        "apiKey": "ollama-local",
        "api": "openai-completions",
        "models": [
          {
            "id": "qwen2.5:7b-32k",
            "name": "Qwen 2.5 7B",
            "reasoning": false,
            "contextWindow": 32768,
            "maxTokens": 32768
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/qwen2.5:7b-32k"
      }
    }
  }
}
```

> **注意**：7B 模型至少需要 8GB 显存。建议先用云端 API 熟悉 OpenClaw，再考虑本地模型。

## 安装 Skills 扩展功能

Skills 是 OpenClaw 的核心竞争力，1700+ 社区技能让 AI 真正能"干活"。

```bash
# 安装技能管理工具
npx clawhub install clawhub

# 安装常用技能
npx clawhub install filesystem-mcp   # 文件操作
npx clawhub install github           # GitHub 集成
npx clawhub install summarize        # 摘要生成
npx clawhub install weather          # 天气查询
npx clawhub install nano-pdf         # PDF 编辑
```

安装后重启 OpenClaw（Ctrl+C 退出 TUI 再重新运行），检查技能状态：

```bash
openclaw skills list
```

装了 Skills 后你就可以用自然语言下指令了，比如：

- "帮我整理今天的 AI 领域早报"
- "监控 GitHub Trending，有意思的项目通知我"
- "总结这个 PDF 文件的要点"

## 常见问题

### Q1：`Model context window too small (4096 tokens)`

OpenClaw 要求模型上下文至少 16000 tokens。解决方法：

1. 打开 `~/.openclaw/openclaw.json`
2. 找到你的模型配置，把 `contextWindow` 和 `maxTokens` 改为 `32768`
3. 重启 OpenClaw

### Q2：`Verification failed` 配置失败

检查三点：
- Ollama 服务是否在运行（执行 `ollama list` 测试）
- API Key 是否填了（不能留空，随便填个字符串）
- Base URL 末尾是否有 `/v1`

### Q3：端口 11434 被占用

Ollama 安装后会自动作为系统服务运行，无需手动启动。直接使用即可。

### Q4：Windows 上哪些 Skills 不能用？

依赖 macOS 专有框架的技能无法使用，如 `apple-notes`、`bear-notes`、`imsg` 等。

## 安全提醒

OpenClaw 虽然强大，但需要注意安全：

1. **升级到最新版本**：旧版本存在已知漏洞（如 CVE-2026-25253）
2. **启用 Token 认证**：不要使用 `auth: "none"` 模式
3. **设置 API 支出限制**：在模型供应商后台设置每月消费上限，防止 Token 超支
4. **定期安全审计**：运行 `openclaw security audit --deep`

## 总结

OpenClaw 的本地部署非常简单：

```
安装 Node.js → 运行安装脚本 → 配置向导 → 打开 Dashboard
```

三步就能跑起来。真正的价值在于 **部署之后怎么用好它**——Skills 怎么选、模型怎么配、工作流怎么设计，这些才是 OpenClaw 发挥作用的关键。

### 参考链接

- OpenClaw 官网：[openclaw.ai](https://openclaw.ai/)
- GitHub 仓库：[github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
- 中文社区汉化版：[GitHub](https://github.com/1186258278/OpenClawChineseTranslation)
- 官方文档：[docs.openclaw.ai](https://docs.openclaw.ai/)

---

*如果你在部署过程中遇到问题，欢迎在评论区留言！*
