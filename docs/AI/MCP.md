# MCP

MCP提供了一个通用的标准，让 AI 模型、AI 客户端（如 Cursor、Claude Desktop）和数据源（本地文件、数据库、API）之间能够互相联系。

## MCP Host (主机)

AI 客户端，比如 **Claude Desktop App**、**Cursor**、**Windsurf** 等 IDE以及 **Cherry Studio** 这样的对话基座。

## MCP Server (服务器/插件)

一个小程序，用来连接具体的服务。比如“读取本地文件”的 Server，“搜索 Google”的 Server，或者“操作 GitHub”的 Server。

## MCP Client (客户端)

连接 Host 和 Server 的桥梁（通常集成在 Host 里）。

## MCP的实际配置

### 下载 MCP Host

例如 Claude Desktop。

### 环境配置

安装 uv、bun等运行环境。（非必须，可参阅文档）

### 修改配置文件

找到 MCP 主机的配置文件，在这个 JSON 文件里加入MCP Server的提供的代码。例如添加 SQLite Server：

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-sqlite",
        "--db-path",
        "/Users/username/test.db" 
      ]
    }
  }
}
```

### 文件系统服务器 (Filesystem Server)

允许 AI 直接读取、修改你电脑指定文件夹里的代码或文档。

### GitHub 服务器 (GitHub Server)

允许 AI 读取你的云端仓库、提交 PR、查看 Issue。

### MCP Server 资源库

**Glama / MCP Servers Directory**

**GitHub 上的 `modelcontextprotocol/servers` 仓库**