# Gemini CLI 扩展

Gemini CLI 支持用于配置和扩展其功能的扩展程序。

## 工作原理

启动时，Gemini CLI 会在以下两个位置查找扩展程序：

1.  `<workspace>/.gemini/extensions`
2.  `<home>/.gemini/extensions`

Gemini CLI 会从这两个位置加载所有扩展程序。如果两个位置存在同名的扩展程序，则工作区目录中的扩展程序优先。

在每个位置中，单个扩展程序都以目录的形式存在，其中包含一个 `gemini-extension.json` 文件。例如：

`<workspace>/.gemini/extensions/my-extension/gemini-extension.json`

### `gemini-extension.json`

`gemini-extension.json` 文件包含扩展程序的配置。该文件具有以下结构：

```json
{
  "name": "my-extension",
  "version": "1.0.0",
  "mcpServers": {
    "my-server": {
      "command": "node my-server.js"
    }
  },
  "contextFileName": "GEMINI.md"
}
```

- `name`: 扩展程序的名称。用于唯一标识扩展程序。该名称应与您的扩展程序目录的名称匹配。
- `version`: 扩展程序的版本。
- `mcpServers`: 要配置的 MCP 服务器的映射。键是服务器的名称，值是服务器配置。这些服务器将在启动时加载，就像在 [`settings.json` 文件](./cli/configuration.zh.md) 中配置的 MCP 服务器一样。如果扩展程序和 `settings.json` 文件都配置了同名的 MCP 服务器，则 `settings.json` 文件中定义的服务器优先。
- `contextFileName`: 包含扩展程序上下文的文件的名称。这将用于从工作区加载上下文。如果未使用此属性，但您的扩展程序目录中存在 `GEMINI.md` 文件，则将加载该文件。

当 Gemini CLI 启动时，它会加载所有扩展程序并合并其配置。如果存在任何冲突，则工作区配置优先。 