# CLI工具

## OpenCode

> The open source AI coding agent.

在运行过一次 opencode 后，会生成一个 ~/.config/opencode 文件夹，里面会有一个 opencode.json，如果希望 json 里面可以编写注释的话，可以把后缀名改成 jsonc（下文 oh-my-opencode 也支持这种格式）。

### 模型配置

在 provider 下添加模型是附加到 provider 上的配置，对 opencode /connect 中该provider已配置的模型没有影响，只是在本地的 opencode.jsonc 中为provider 添加了一个新的模型配置。

实际上，/connect 中的模型列表可能是从系统默认配置或其他地方获取的，而本地的配置是个人化的，只会影响本地 opencode 使用的模型选项。

例如：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "nvidia": {
      "options": {
        "baseURL": "https://integrate.api.nvidia.com/v1"
      },
      "models": {
        "deepseek-ai/deepseek-v3.2": {
          "name": "DeepSeek v3.2",
          "limit": {
            "context": 128000,
            "output": 65536
          }
        }
      }
    }
  }
}
```

### Agent

在配置文件中加入"agent"，可以创建一个专门的 agent：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "code-reviewer": {
      "description": "审查代码的最佳实践和潜在问题",
      "model": "anthropic/claude-sonnet-4-5",
      "prompt": "你是一个代码审查员。专注于安全性、性能和可维护性。",
      "tools": {
        // 为仅审查的代理禁用文件修改工具
        "write": false,
        "edit": false
      }
    }
  }
}
```

### 权限

默认情况下，opencode 允许所有操作而无需明确批准。你可以使用 permission 选项更改此设置。

例如，要确保 edit 和 bash 工具需要用户批准：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "ask",
    "bash": "ask"
  }
}
```

## Oh My OpenCode

> 一个异步子代理工具，配备合适的模型，内置 LSP/AST 等精心打造的工具，精选了 MCP 工具集，具有 Claude Code 兼容层，可以自动读取 Claude 配置

让OpenCode帮助安装：在对话框中输入`Install and configure by following the instructions here https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/refs/heads/master/README.md"`