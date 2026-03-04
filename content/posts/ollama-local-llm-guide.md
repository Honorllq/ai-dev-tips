---
title: "Ollama 完全指南：在你自己的电脑上跑大模型"
date: 2026-02-27
draft: false
tags: ["Ollama", "大模型", "本地部署", "AI工具"]
categories: ["AI工具教程"]
summary: "Ollama 让你在自己的电脑上运行 Llama、Qwen、DeepSeek、Gemma 等开源大模型，完全离线、完全免费、数据完全私密。本文手把手教你从安装到高级配置。"
---

想用大模型但不想把数据交给 OpenAI 或 Google？想在飞机上也能用 AI？想零成本使用 AI？

**Ollama** 是目前最简单的本地大模型运行工具。一条命令安装，一条命令跑模型，完全免费。

## Ollama 是什么

Ollama 是一个开源框架，让你在个人电脑上运行大语言模型（LLM）。它底层基于 llama.cpp，把模型下载、配置、GPU 加速、API 服务全部打包成一个简单的命令行工具。

**核心特点**：
- 数据完全在本地，不经过任何服务器
- 自动检测 GPU 并加速
- 提供本地 REST API（兼容 OpenAI 格式）
- 支持 NVIDIA、AMD、Apple Silicon、Intel GPU
- 完全免费开源

## 安装 Ollama

### macOS

```bash
brew install ollama
```

或者去 [ollama.com](https://ollama.com) 下载 .dmg 安装包。

### Windows

```powershell
irm https://ollama.com/install.ps1 | iex
```

或者去 [ollama.com](https://ollama.com) 下载 OllamaSetup.exe。

要求：Windows 10 22H2 或更新版本。

### Linux

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

安装完成后验证：

```bash
ollama --version
```

## 选择合适的模型

Ollama 支持上百个模型，以下是 2026 年最值得跑的模型。

### 按用途选择

| 用途 | 推荐模型 | 大小 | 最低显存 |
|------|---------|------|---------|
| 日常对话 | qwen3:8b | ~5GB | 8GB |
| 中文对话 | qwen2.5:14b | ~9GB | 12GB |
| 推理/数学 | deepseek-r1:14b | ~9GB | 12GB |
| 编程辅助 | deepseek-r1:32b | ~19GB | 24GB |
| 轻量对话 | gemma3:4b | ~2.5GB | 4GB |
| 手机/树莓派 | llama3.2:1b | ~0.7GB | 2GB |

### 按显存选择

| 你的显存 | 推荐模型 |
|---------|---------|
| 4GB | gemma3:4b, llama3.2:3b |
| 6-8GB | llama3.1:8b, qwen3:8b, deepseek-r1:7b |
| 10-12GB | qwen3:14b, gemma3:12b, deepseek-r1:14b |
| 16-24GB | qwen3:32b, gemma3:27b, deepseek-r1:32b |
| 48GB+ | llama3.3:70b, qwen2.5:72b |

**经验法则**：模型文件大小不要超过可用显存的 2/3。超出显存的部分会溢出到内存/CPU，速度会降低 5-30 倍。

## 基本使用

### 下载并运行模型

```bash
# 下载并启动交互对话
ollama run qwen3:8b

# 单次提问
ollama run qwen3:8b "用Python实现二分查找"
```

### 常用命令

```bash
# 查看已下载的模型
ollama list

# 查看正在运行的模型（及 GPU/CPU 分配）
ollama ps

# 下载模型（不启动）
ollama pull deepseek-r1:14b

# 删除模型
ollama rm gemma3:4b

# 停止运行中的模型（释放显存）
ollama stop qwen3:8b

# 查看模型详细信息
ollama show qwen3:8b
```

### 对话中的命令

在 `ollama run` 的对话模式中：

```
/set parameter num_ctx 8192    # 设置上下文窗口
/set parameter temperature 0.7  # 设置温度
/set system "你是一个Python专家" # 设置系统提示
/show info                      # 查看模型信息
/bye                            # 退出
```

## API 调用

Ollama 启动后自动在 `http://localhost:11434` 提供 REST API。

### 对话接口

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "qwen3:8b",
  "messages": [
    {"role": "user", "content": "什么是快速排序？"}
  ],
  "stream": false
}'
```

### 文本生成接口

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "qwen3:8b",
  "prompt": "解释什么是Docker",
  "stream": false
}'
```

### 生成向量嵌入

```bash
curl http://localhost:11434/api/embed -d '{
  "model": "nomic-embed-text",
  "input": "机器学习是人工智能的子领域"
}'
```

### Python 调用

```python
# 方式一：Ollama 官方 SDK
from ollama import chat

response = chat(model='qwen3:8b', messages=[
    {'role': 'user', 'content': '用三句话解释量子计算'},
])
print(response.message.content)
```

```python
# 方式二：兼容 OpenAI SDK
from openai import OpenAI

client = OpenAI(
    api_key="ollama",
    base_url="http://localhost:11434/v1"
)

response = client.chat.completions.create(
    model="qwen3:8b",
    messages=[{"role": "user", "content": "你好"}]
)
print(response.choices[0].message.content)
```

### JavaScript 调用

```javascript
import ollama from "ollama";

const response = await ollama.chat({
  model: "qwen3:8b",
  messages: [{ role: "user", content: "Hello!" }],
});
console.log(response.message.content);
```

## 自定义模型（Modelfile）

Modelfile 让你基于现有模型创建定制版本。

### 创建一个 Python 助手

```dockerfile
# 文件名：Modelfile
FROM qwen3:8b

SYSTEM """你是一个资深Python开发者。
你的回答简洁精准，总是附带代码示例。
你偏好使用现代Python（3.11+）特性。"""

PARAMETER temperature 0.3
PARAMETER num_ctx 8192
```

构建并运行：

```bash
ollama create python-helper -f Modelfile
ollama run python-helper
```

### 创建大上下文版本

有些场景需要更大的上下文窗口：

```dockerfile
FROM qwen2.5:7b
PARAMETER num_ctx 32768
```

```bash
ollama create qwen2.5:7b-32k -f Modelfile
```

### Modelfile 可用参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| temperature | 创造性（0=确定性，1=随机） | 0.8 |
| num_ctx | 上下文窗口长度 | 4096 |
| top_p | 核采样概率 | 0.9 |
| top_k | Top-K 采样 | 40 |
| repeat_penalty | 重复惩罚 | 1.1 |
| num_predict | 最大生成 token 数 | -1（无限） |

## GPU 加速配置

### NVIDIA 显卡

Ollama 自动检测 NVIDIA GPU（需要安装驱动 452.39+）。验证：

```bash
# 运行模型后检查 GPU 使用
ollama ps
# 应显示 100% GPU
```

### 多 GPU 配置

```bash
# 只使用第一张 GPU
CUDA_VISIBLE_DEVICES=0 ollama serve

# 使用两张 GPU
CUDA_VISIBLE_DEVICES=0,1 ollama serve

# 均匀分配到所有 GPU
OLLAMA_SCHED_SPREAD=1 ollama serve
```

### Apple Silicon（M 系列芯片）

macOS 上 Ollama 自动使用 Metal API 加速，无需额外配置。M1 以上的 Mac 可以流畅运行 7B-14B 模型。

### 强制 CPU 运行

```bash
CUDA_VISIBLE_DEVICES=-1 ollama serve
```

## 性能优化技巧

### 1. 开启 Flash Attention

```bash
# Linux/macOS
export OLLAMA_FLASH_ATTENTION=1

# Windows PowerShell
$env:OLLAMA_FLASH_ATTENTION="1"
```

### 2. KV 缓存量化

需要先开启 Flash Attention：

```bash
export OLLAMA_KV_CACHE_TYPE=q8_0   # 缓存内存减半
# 或
export OLLAMA_KV_CACHE_TYPE=q4_0   # 缓存内存减 75%（有质量损失）
```

### 3. 减小上下文窗口

默认 4096，如果不需要长对话可以减小：

```bash
OLLAMA_CONTEXT_LENGTH=2048 ollama serve
```

### 4. 预加载模型

避免每次请求都重新加载：

```bash
# 预加载并保持在内存
curl http://localhost:11434/api/generate -d '{"model": "qwen3:8b", "keep_alive": -1}'

# 用完后释放
curl http://localhost:11434/api/generate -d '{"model": "qwen3:8b", "keep_alive": 0}'
```

### 5. 量化选择建议

| 量化等级 | 质量 | 大小 | 推荐 |
|---------|------|------|------|
| Q2 | 差 | 最小 | 不推荐 |
| Q3_K_M | 可用 | 小 | 显存极有限时 |
| **Q4_K_M** | **好** | **中等** | **推荐，最佳平衡** |
| Q5_K_M | 很好 | 较大 | 有余量时 |
| Q6_K | 优秀 | 大 | 追求质量 |
| Q8_0 | 近乎无损 | 很大 | 显存充足时 |

## 搭配 Web UI 使用

纯命令行不习惯？可以搭配 Web UI 获得 ChatGPT 般的体验。

### Open WebUI（最流行）

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  ghcr.io/open-webui/open-webui:main
```

然后打开 `http://localhost:3000`，界面类似 ChatGPT。

### Docker Compose 一键部署

```yaml
version: '3.8'
services:
  ollama:
    image: ollama/ollama:latest
    ports:
      - "11434:11434"
    volumes:
      - ollama_models:/root/.ollama
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: all
              capabilities: [gpu]
    restart: unless-stopped

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3000:8080"
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434
    volumes:
      - open_webui_data:/app/backend/data
    depends_on:
      - ollama
    restart: unless-stopped

volumes:
  ollama_models:
  open_webui_data:
```

## 与其他工具集成

Ollama 可以作为后端连接各种 AI 工具：

| 工具 | 用途 | 配置方式 |
|------|------|---------|
| **OpenClaw** | AI 管家 | 配置 Ollama 为模型提供商 |
| **Continue** | VS Code AI 编程 | 设置 Ollama 后端 |
| **LangChain** | AI 应用开发 | `ChatOllama` 类 |
| **AnythingLLM** | RAG 知识库 | 连接 Ollama API |

## Ollama vs LM Studio

| 维度 | Ollama | LM Studio |
|------|--------|-----------|
| 界面 | CLI + API | GUI 桌面应用 |
| 适合 | 开发者、API 集成 | 新手、图形化操作 |
| 模型格式 | GGUF | GGUF |
| 开源 | 是 | 否（免费但闭源） |
| Docker | 支持 | 不支持 |
| 多用户 | 支持 | 不支持 |

**建议**：如果你是开发者或需要 API 集成，选 Ollama；如果你只想图形界面聊天，LM Studio 更友好。

## 常见问题

### Q1：GPU 没被识别

NVIDIA 用户检查驱动版本（>= 452.39），运行 `ollama ps` 查看是否显示 "100% GPU"。

### Q2：运行很慢

大概率是模型溢出到了 CPU。解决方案：换更小的模型，或用更高量化压缩。

### Q3：模型存储位置

| 系统 | 默认路径 |
|------|---------|
| macOS | `~/.ollama/models` |
| Linux | `/usr/share/ollama/.ollama/models` |
| Windows | `C:\Users\用户名\.ollama\models` |

修改存储位置：设置环境变量 `OLLAMA_MODELS=/你的路径`

### Q4：如何让局域网内其他设备访问

```bash
OLLAMA_HOST=0.0.0.0:11434 ollama serve
```

然后其他设备访问 `http://你的IP:11434`。

### Q5：完全断网能用吗

可以。模型下载到本地后，Ollama 不需要网络就能运行。

## 常用环境变量速查

| 变量 | 说明 | 默认 |
|------|------|------|
| `OLLAMA_HOST` | 监听地址 | 127.0.0.1:11434 |
| `OLLAMA_MODELS` | 模型存储路径 | ~/.ollama/models |
| `OLLAMA_KEEP_ALIVE` | 模型在内存中的保留时间 | 5m |
| `OLLAMA_FLASH_ATTENTION` | 开启 Flash Attention | false |
| `OLLAMA_KV_CACHE_TYPE` | KV 缓存量化 | f16 |
| `OLLAMA_NUM_PARALLEL` | 最大并行请求数 | 1 |
| `OLLAMA_CONTEXT_LENGTH` | 默认上下文长度 | 4096 |
| `OLLAMA_DEBUG` | 调试日志 | 0 |

## 总结

Ollama 把"本地跑大模型"这件事做到了极致简单：

```
安装 → 一条命令
跑模型 → 一条命令
API 调用 → 兼容 OpenAI
Web UI → Docker 一键部署
```

不花钱、不泄露数据、不需要网络——这就是 Ollama 的核心价值。

### 参考链接

- Ollama 官网：[ollama.com](https://ollama.com)
- GitHub：[github.com/ollama/ollama](https://github.com/ollama/ollama)
- 模型库：[ollama.com/library](https://ollama.com/library)
- Open WebUI：[github.com/open-webui/open-webui](https://github.com/open-webui/open-webui)

---

*你在本地跑什么模型？欢迎在评论区分享你的配置和体验！*
