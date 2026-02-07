# CLI工具

> 轻量、灵活、适合DevOps场景

## OpenCode

> The open source AI coding agent.

在运行过一次 opencode 后，会生成一个 `~/.config/opencode` 文件夹，可以新建一个 `opencode.json`，如果希望 json 里面可以编写注释的话，可以把后缀名改成 jsonc（下文 oh-my-opencode 也支持这种格式）。

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

#### API Key 配置

使用 /connect 命令添加提供商的 API 密钥时，它们会存储在 `~/.local/share/opencode/auth.json` 中。

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

可以通过@命令调用子代理，例如：`@code-reviewer 这段代码有什么问题吗？`

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

### Oh My OpenCode

> 一个异步子代理工具，配备合适的模型，内置 LSP/AST 等精心打造的工具，精选了 MCP 工具集，具有 Claude Code 兼容层，可以自动读取 Claude 配置

让OpenCode帮助安装：在对话框中输入`Install and configure by following the instructions here https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/refs/heads/master/README.md"`

安装完成后，启动 OpenCode，`~/.config/opencode`配置目录里多了 oh-my-opencode.json，这里可以配置每个子 Agent 使用什么模型。例如：

```jsonc
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/master/assets/oh-my-opencode.schema.json",
  "agents": {
    "Sisyphus": {// 西西弗斯，负责协调和直接执行简单的任务
      "model": "antigravity-api/gemini-3-flash"
    },
    "librarian": {// 帮助你查找库相关的信息
      "model": "volcengine-ark-api/deepseek-v3-2-251201"
    },
    "explore": {// 探索现有代码仓库
      "model": "antigravity-api/claude-sonnet-4-5"
    },
    "oracle": {// 先知，执行困难的任务和debug
      "model": "antigravity-api/claude-opus-4-5-thinking"
    },
    "frontend-ui-ux-engineer": {// 前端设计师
      "model": "antigravity-api/gemini-3-pro-high"
    },
    "document-writer": {// 写文档的模型
      "model": "volcengine-ark-api/deepseek-v3-2-251201"
    },
    "multimodal-looker": {// 多模态识别
      "model": "antigravity-api/gemini-3-flash"
    }
  }
}
```

## Claude Code

### 安装

```bash
npm install -g @anthropic-ai/claude-code
```

### 命令

- 确认目前的 Provider：`/status`
- 总结上下文：对话太长，输入 `/compact` 清理掉不必要的历史数据。
- 彻底重置上下文：开启一个新任务前，使用 `/clear` 彻底重置上下文。

### 子代理

使用 `/agent` 命令创建子代理。可以创建用户代理与项目代理。用户代理的位置在 `~/.claude-code/agents/`，项目代理的位置在项目根目录的 `.claude-code/agents/`。

### 插件

#### oh-my-claudecode

仓库地址：<https://github.com/Yeachan-Heo/oh-my-claudecode>

魔法关键词：

- autopilot：完全自主执行
- ralph：持久模式 
- ulw：最大并行度 
- eco：令牌高效执行 
- plan：规划面向执行
- 合并它们： ralph ulw = 持久性 + 并行性

## Codex CLI

在白嫖（bushi）Claude Code API失败（好几个公益站都有时报错）后，我转而使用 Codex CLI，因为Codex的模型支持更稳定。

### 配置

在`~/.codex`目录下，Codex的配置文件夹结构如下：

```plaintext
.codex/
  ├── AGENTS.md           # 全局指令
  ├── AGENTS.override.md  # 覆盖配置（优先级更高）
  ├── prompts/            # 自定义提示词模板
  │   ├── check-fix.md
  │   ├── refactor.md
  │   └── ...
  ├── skills/             # 自定义技能（类似 slash commands）
  │   ├── play/
  │   │   └── SKILL.md
  │   ├── mindmap/
  │   │   └── SKILL.md
  │   ├── plan/
  │   │   └── SKILL.md
  │   └── prompt/
  │       └── SKILL.md
  └── sessions/           # 会话历史
```

### AGENTS.md

全局指令文件，可以让其它AI撰写指令。例如：

```markdown
- Always reply in Chinese.
- 除非用户明确要求英文，否则所有回复使用简体中文。
- 代码标识符、命令、日志、报错信息保持原始语言；其余解释用中文。
```

### prompts

prompts 目录下可以放自定义的提示词模板，用法是 `/提示词名称`。例如：`/check-fix`，用来在改完 bug 之后做影响分析，防止改了一处炸了三处：

```markdown
# 修复影响检查 (Fix Impact Analysis)

对当前修改进行全面影响分析，检查是否对其他逻辑造成破坏。

## 检查维度

### 1️⃣ 直接影响分析
- 修改的函数/方法被哪些地方调用？
- 修改的参数签名是否向后兼容？
- 返回值类型/结构是否发生变化？

### 2️⃣ 间接影响分析
- 调用链上下游的数据流向
- 共享状态/全局变量的修改
- 事件监听器/回调函数的触发时机

### 3️⃣ 数据结构兼容性
- 新增字段: 旧数据读取时是否有默认值？
- 删除字段: 是否有代码仍在访问该字段？
- 类型变更: string→number 等隐式转换
```

### skills

skills 目录下可以放自定义的技能（类似 slash commands）。例如：`/play`，用来自动化浏览器操作：

```markdown
# 打开链接（Playwright 自动化）

**核心任务**：使用 Playwright MCP 工具自动化浏览器操作。

## 执行步骤
1. 打开链接（browser_navigate）
2. 获取页面状态（browser_snapshot）
3. 处理登录逻辑（如果需要）
4. 遇到问题立即停止并告知
```