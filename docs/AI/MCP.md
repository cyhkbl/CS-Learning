# MCP

> MCP提供了一个通用的标准，让 AI 模型、AI 客户端（如 Cursor、Claude Desktop）和数据源（本地文件、数据库、API）之间能够互相联系。

## MCP Host (主机)

AI 客户端，比如 **Claude Desktop App**、**Cursor**、**Windsurf** 等 IDE以及 **Cherry Studio** 这样的对话基座。

## MCP Server (服务器/插件)

一个小程序，用来连接具体的服务。比如“读取本地文件”的 Server，“搜索 Google”的 Server，或者“操作 GitHub”的 Server。

## MCP Client (客户端)

连接 Host 和 Server 的桥梁（通常集成在 Host 里）。

## MCP配置

### 下载 MCP Host

例如 Claude Desktop。

### 环境配置

安装 uv、bun等运行环境。（非必须，可参阅文档）

### 修改配置文件

找到 MCP 主机的配置文件，在这个 JSON 文件里加入MCP Server的提供的代码。例如添加 SQLite Server：

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": [
        "@playwright/mcp@latest"
      ]
    }
  }
}
```

由于Windows系统需要命令解释器（cmd.exe或powershell.exe）来执行大多数脚本命令，当使用npx @playwright/mcp@latest时，可能会报错：npx 不是独立的可执行文件，它是npm包中的一个JavaScript脚本。Windows无法直接解析npx命令，需要cmd.exe来解释它。正确的调用方式应该是：`cmd /c npx @playwright/mcp@latest`

因此，在Windows中配置需要加入 `/c npx` 参数，应该改为：

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": [
        "/c",
        "npx",
        "@playwright/mcp@latest"
      ]
    }
  }
}
``` 