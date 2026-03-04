---
title: "MCP 协议详解：AI 的「USB-C」接口标准"
date: 2026-02-28
draft: false
tags: ["MCP", "AI Agent", "Claude Code", "协议"]
categories: ["AI工具教程"]
series: ["AI编程工具系列"]
keywords: ["MCP协议", "MCP教程", "Model Context Protocol", "MCP Server", "Claude Code MCP"]
summary: "MCP（Model Context Protocol）是 Anthropic 推出的 AI 工具连接开放标准，被称为「AI 的 USB-C」。本文详解 MCP 的架构原理、配置方法、热门服务器，以及如何自己构建 MCP Server。"
faq:
  - q: "MCP 是什么？"
    a: "MCP（Model Context Protocol）是 Anthropic 推出的开放标准，让 AI 模型能安全连接外部工具和数据源。类似 USB-C 统一了充电接口，MCP 统一了 AI 的工具连接方式。"
  - q: "哪些工具支持 MCP？"
    a: "Cursor、Claude Code、GitHub Copilot、Windsurf、VS Code 都已支持 MCP。OpenAI、Google、Microsoft 也已采纳。目前有 18,000+ 个注册 MCP Server。"
  - q: "MCP Server 怎么开发？"
    a: "用 Python 的 FastMCP 或 TypeScript SDK，几十行代码就能构建一个 MCP Server。详细教程见本文的'自己构建 MCP Server'章节。"
---

2024 年 11 月，Anthropic 开源了一个协议，悄悄改变了整个 AI 行业的工具连接方式。到 2025 年底，OpenAI、Google、Microsoft 全部跟进采纳。这个协议就是 **MCP（Model Context Protocol）**。

## MCP 是什么

MCP 是一个**开放标准**，让 AI 模型能够安全地连接外部数据源和工具。

**一句话理解**：就像 USB-C 统一了所有设备的充电和数据接口，MCP 统一了 AI 连接工具的方式。

### 它解决了什么问题

没有 MCP 之前：5 个 AI 工具 × 3 个 AI 模型 = **15 个**独立适配器。

有了 MCP 之后：5 个工具 + 3 个模型 = **8 个**标准化连接。

规模越大优势越明显：20 个工具 × 4 个模型，从 80 个适配器减少到 24 个。

## MCP 架构

MCP 采用**客户端-服务器**架构，有三个核心角色：

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   MCP Host   │     │  MCP Client  │     │  MCP Server  │
│ (Claude Code │────▶│ (连接管理器)  │────▶│ (工具/数据源) │
│  Cursor 等)  │     │              │     │              │
└─────────────┘     └─────────────┘     └─────────────┘
```

- **Host**：AI 应用（如 Claude Code、Cursor、VS Code）
- **Client**：维持与 MCP Server 的连接
- **Server**：提供工具、数据和提示模板

### 三大核心能力

| 类型 | 说明 | 例子 |
|------|------|------|
| **Tools** | 可执行的函数 | 搜索文件、查询数据库、发送消息 |
| **Resources** | 数据源 | 文件内容、数据库记录、API 响应 |
| **Prompts** | 可复用的提示模板 | 系统提示词、Few-shot 示例 |

### 通信方式

1. **Stdio**：标准输入输出，用于本地进程通信，零网络开销
2. **Streamable HTTP**：HTTP POST + Server-Sent Events，支持远程连接和 OAuth 认证

所有通信基于 **JSON-RPC 2.0** 协议。

## MCP vs 传统 API

| 维度 | MCP | 传统 API |
|------|-----|---------|
| 工具发现 | 运行时动态发现 | 必须先读文档 |
| 集成模型 | M + N（标准化） | M × N（逐一适配） |
| 通信格式 | AI 友好的 JSON 描述 | 开发者导向的 REST |
| 新增工具 | 创建 Server 即可用 | 需要开发适配器 |
| 认证 | 标准化 OAuth 2.1 | 每个 API 各自实现 |
| 延迟 | 有协议层开销 | 直接调用，最小开销 |

**结论**：MCP 适合 AI Agent 工作流；传统 API 适合高性能、低延迟的直接调用场景。

## 在 Claude Code 中配置 MCP

Claude Code 支持三个配置范围：

### 1. 本地配置（默认）

```bash
# 添加 HTTP 类型的 MCP Server
claude mcp add --transport http stripe https://mcp.stripe.com

# 添加 Stdio 类型的 MCP Server
claude mcp add --transport stdio github -- npx -y @modelcontextprotocol/server-github

# 带环境变量
claude mcp add --transport stdio --env GITHUB_TOKEN=your_token github -- npx -y @modelcontextprotocol/server-github
```

### 2. 项目配置（`.mcp.json`）

创建 `.mcp.json` 放在项目根目录，团队共享：

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "./src"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

### 3. 用户配置（全局）

```bash
claude mcp add --transport http --scope user my-server https://my-server.com/mcp
```

### 常用管理命令

```bash
claude mcp list              # 查看所有 MCP Server
claude mcp get github        # 查看某个 Server 详情
claude mcp remove github     # 移除 Server
claude mcp add-from-claude-desktop  # 从 Claude Desktop 导入
```

在对话中输入 `/mcp` 可以实时查看 MCP 状态。

## 热门 MCP Server

### 官方参考服务器

| Server | 用途 |
|--------|------|
| Filesystem | 安全的文件操作 |
| Git | Git 仓库读写和搜索 |
| Memory | 知识图谱持久化记忆 |
| Fetch | 网页内容抓取 |
| Sequential Thinking | 动态问题解决 |

### 企业官方维护

| Server | 公司 | 用途 |
|--------|------|------|
| GitHub | GitHub | 仓库、PR、Issue 管理 |
| Slack | Slack | 频道管理和消息 |
| Notion | Notion | 工作空间数据 |
| Stripe | Stripe | 支付操作 |
| Linear | Linear | 问题追踪 |
| Shopify | Shopify | 电商管理 |

### 社区热门

| Server | 用途 |
|--------|------|
| PostgreSQL | 数据库访问 |
| SQLite | 轻量数据库 |
| Puppeteer | 浏览器自动化 |
| Brave Search | 网页搜索 |
| Docker | 容器管理 |

### 发现平台

- **MCP.so**：收录 18,000+ 个 MCP Server
- **MCPServers.org**：精选合集
- **Awesome MCP Servers**：GitHub 社区整理

## 自己构建 MCP Server

### Python 版（FastMCP）

```bash
pip install mcp
```

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("我的工具")

@mcp.tool()
def search_docs(query: str) -> str:
    """搜索文档库中的内容。"""
    # 你的搜索逻辑
    return f"搜索 '{query}' 的结果..."

@mcp.resource("config://app")
def get_config() -> dict:
    """获取应用配置。"""
    return {"version": "1.0", "name": "MyApp"}

if __name__ == "__main__":
    mcp.run(transport="stdio")
```

### TypeScript 版

```bash
npm install @modelcontextprotocol/sdk zod
```

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-tools",
  version: "1.0.0",
});

server.tool(
  "search-docs",
  "搜索文档库中的内容",
  { query: z.string().describe("搜索关键词") },
  async ({ query }) => ({
    content: [{ type: "text", text: `搜索 '${query}' 的结果...` }],
  })
);

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
}

main().catch(console.error);
```

### 测试 MCP Server

```bash
npx -y @modelcontextprotocol/inspector
```

这会启动一个 Web 调试工具，方便测试你的 MCP Server。

## MCP 生态增长

| 时间 | 事件 |
|------|------|
| 2024.11 | Anthropic 开源 MCP |
| 2025.3 | OpenAI 在 Agents SDK 中采纳 MCP |
| 2025.4 | Google DeepMind 确认 Gemini 支持 MCP；VS Code、GitHub 集成 |
| 2025.5 | Microsoft 加入 MCP 指导委员会；AWS 在 Bedrock 中支持 |
| 2025.12 | Anthropic 将 MCP 捐赠给 Linux 基金会下的 AAIF |

**关键数据**：
- SDK 月下载量：从 10 万增长到 800+ 万
- 注册 Server 数：18,000+
- 可用客户端：300+

## 安全注意事项

MCP 的开放性也带来安全风险：

1. **Prompt 注入**：恶意数据源中植入隐藏指令
2. **工具投毒**：在工具描述中嵌入有害命令
3. **权限滥用**：工具获得过多权限

### 防护建议

- 只安装来自可信源的 MCP Server
- 使用 OAuth 2.1 进行认证
- 在 Docker 容器中运行 MCP Server（沙箱隔离）
- 定期审计已安装的 Server
- 企业环境使用 `managed-mcp.json` 统一管控

## 总结

MCP 正在成为 AI 工具连接的事实标准。作为开发者，你需要了解：

1. **使用者**：学会在 Claude Code / Cursor 中配置 MCP Server
2. **构建者**：用 FastMCP 几十行代码就能构建自己的 Server
3. **架构师**：理解 MCP 如何改变 AI Agent 的工具连接方式

### 参考资料

- MCP 官网：[modelcontextprotocol.io](https://modelcontextprotocol.io)
- MCP 规范：[modelcontextprotocol.io/specification](https://modelcontextprotocol.io/specification/2025-11-25)
- Python SDK：[github.com/modelcontextprotocol/python-sdk](https://github.com/modelcontextprotocol/python-sdk)
- Server 合集：[github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)
- MCP 中文站：[mcpcn.com](https://mcpcn.com)

---

## 延伸阅读

- [Cursor vs Claude Code vs Copilot vs Windsurf](/posts/cursor-vs-claude-code-vs-copilot-vs-windsurf/) — 四大支持 MCP 的 AI 编程工具对比
- [AI Agent 赚钱变现：9 种已验证的方法](/posts/ai-agent-monetization/) — MCP Server 开发是其中一种变现方式
- [程序员转型 AI：2026 年完整学习路径](/posts/programmer-ai-learning-path/) — MCP 是 AI 工程化的核心技能

---

*你用 MCP 连接了什么工具？欢迎在评论区分享！*
