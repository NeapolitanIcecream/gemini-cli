# 教程

本页包含与 Gemini CLI 交互的教程。

## 搭建 Model Context Protocol (MCP) 服务器

> [!CAUTION]
> 在使用第三方 MCP 服务器之前，请确保你信任其来源并理解其提供的工具。使用第三方服务器风险自负。

本教程演示如何搭建 MCP 服务器，以 [GitHub MCP 服务器](https://github.com/github/github-mcp-server) 为例。该服务器提供了与 GitHub 仓库交互的工具，如创建 Issue 和在拉取请求中评论。

### 前置条件

在开始之前，请确保已安装并配置以下内容：

- **Docker：** 安装并运行 [Docker]。
- **GitHub Personal Access Token (PAT)：** 创建新的[classic] 或 [fine-grained] PAT，并具有必要的权限。

[Docker]: https://www.docker.com/
[classic]: https://github.com/settings/tokens/new
[fine-grained]: https://github.com/settings/personal-access-tokens/new

### 指南

#### 在 `settings.json` 中配置 MCP 服务器

在项目根目录下创建或打开[`.gemini/settings.json` 文件](./configuration.zh.md)。在文件中添加 `mcpServers` 配置块，用于提供启动 GitHub MCP 服务器的指令。

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PERSONAL_ACCESS_TOKEN}"
      }
    }
  }
}
```

#### 设置 GitHub 令牌

> [!CAUTION]
> 使用具有广泛作用域、同时访问个人和私有仓库的个人访问令牌，可能导致私有仓库信息泄漏到公共仓库。建议使用不同时访问公共与私有仓库的细粒度令牌。

使用环境变量存储 GitHub PAT：

```bash
GITHUB_PERSONAL_ACCESS_TOKEN="pat_YourActualGitHubTokenHere"
```

Gemini CLI 会在你在 `settings.json` 文件中定义的 `mcpServers` 配置中使用此值。

#### 启动 Gemini CLI 并验证连接

启动 Gemini CLI 时，它会自动读取配置并在后台启动 GitHub MCP 服务器。此后，你可以使用自然语言提示让 Gemini CLI 执行 GitHub 操作。例如：

```bash
"get all open issues assigned to me in the 'foo/bar' repo and prioritize them"
```