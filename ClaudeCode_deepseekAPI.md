## Claude Code + deepseek API 

*Updated 2026-06-15 22:03 GMT+8*  
 *Compiled by Hongfei Yan (2026 summer)*  



在mac机器上，执行

npm install -g @anthropic-ai/claude-code

claude --version

> 如果报错，执行
>
> node -p process.arch
>
> npm install -g @anthropic-ai/claude-code-darwin-arm64
>
> node $(npm root -g)/@anthropic-ai/claude-code/install.cjs





以下是关于 **Claude Code 接入 DeepSeek** 的完整指南，分两个场景：

------

## 方案一：用 DeepSeek 替换 Claude Code 的底层模型

DeepSeek 提供了与 Anthropic API 兼容的接口，可以直接通过设置环境变量，让 Claude Code 调用 DeepSeek 模型作为后端。

### 第一步：获取 DeepSeek API Key

前往 [platform.deepseek.com](https://platform.deepseek.com/) 注册并创建 API Key。

### 第二步：配置环境变量

**Linux / macOS：**

```bash
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_AUTH_TOKEN=<your DeepSeek API Key>
export ANTHROPIC_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_OPUS_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_SONNET_MODEL=deepseek-v4-pro[1m]
export ANTHROPIC_DEFAULT_HAIKU_MODEL=deepseek-v4-flash
export CLAUDE_CODE_SUBAGENT_MODEL=deepseek-v4-flash
export CLAUDE_CODE_EFFORT_LEVEL=max
```

**Windows（PowerShell）：**

```powershell
$env:ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
$env:ANTHROPIC_AUTH_TOKEN="<your DeepSeek API Key>"
$env:ANTHROPIC_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_OPUS_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL="deepseek-v4-pro[1m]"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL="deepseek-v4-flash"
$env:CLAUDE_CODE_SUBAGENT_MODEL="deepseek-v4-flash"
$env:CLAUDE_CODE_EFFORT_LEVEL="max"
```

也可以写入 `~/.claude/settings.json` 持久化配置：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "<your DeepSeek API Key>",
    "ANTHROPIC_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1",
    "CLAUDE_CODE_EFFORT_LEVEL": "max"
  }
}
```

### 模型映射说明

DeepSeek 的 Anthropic 兼容接口会自动映射模型名称：`claude-opus` 开头的模型映射到 `deepseek-v4-pro`，`claude-haiku` 或 `claude-sonnet` 开头的映射到 `deepseek-v4-flash`。

注意：不能直接传 `--model deepseek-v4-pro`，Claude Code 会校验模型名称白名单，建议用 `--model sonnet` 或 `--model opus`，DeepSeek 端会在服务侧完成映射。

### 第三步：正常启动 Claude Code

```bash
claude
```

此后所有 API 请求都会路由到 DeepSeek。

------

## 方案二：在 Cowork 中接入 DeepSeek

Claude Cowork 是 Anthropic 面向知识工作的 AI Agent，可以使用电脑、本地文件和应用程序来完成复杂任务。通过 Composio Connect，Cowork 可以安全地通过 MCP 协议访问 DeepSeek，而无需直接共享账户凭据。

**接入方式：**

1. 在 Cowork 中，直接让 Agent 执行 DeepSeek 相关任务，它会提示你完成认证授权
2. 或者通过 Composio 配置 MCP 服务器

```bash
claude mcp add --transport http deepseek-composio "YOUR_MCP_URL" \
  --headers "X-API-Key:YOUR_COMPOSIO_API_KEY"
```

------

## 两种模型的分工建议

DeepSeek-V4-Flash 适合高速低延迟任务，如文件整理、网页搜索、文档处理、简单重构；DeepSeek-V4-Pro 适合高级推理和重度编码任务。

| 用途               | 推荐模型          |
| ------------------ | ----------------- |
| 子 Agent、辅助任务 | deepseek-v4-flash |
| 主 Agent、复杂推理 | deepseek-v4-pro   |

------

完整官方文档参考：[DeepSeek × Claude Code 集成指南](https://api-docs.deepseek.com/quick_start/agent_integrations/claude_code)