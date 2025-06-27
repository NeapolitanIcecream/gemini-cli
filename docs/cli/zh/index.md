# Gemini CLI

在 Gemini CLI 中，`packages/cli` 目录是用户与 Gemini AI 模型及其相关工具交互的前端。若想了解 Gemini CLI 的整体概览，请参阅[主文档页面](../index.md)。

## 如何阅读本节

- **[身份验证](./authentication.md)：** 设置与 Google AI 服务身份验证的指南。
- **[命令](./commands.md)：** Gemini CLI 命令参考（例如 `/help`、`/tools`、`/theme`）。
- **[配置](./configuration.md)：** 通过配置文件定制 Gemini CLI 行为的指南。
- **[令牌缓存](./token-caching.md)：** 通过令牌缓存优化 API 成本。
- **[主题](./themes.md)**：自定义 CLI 外观主题的指南。
- **[教程](./tutorials.md)**：演示如何使用 Gemini CLI 自动化开发任务的教程。

## 非交互模式

Gemini CLI 可以在非交互模式下运行，这对于脚本编写和自动化非常有用。在此模式中，你将输入通过管道传递给 CLI，CLI 执行命令后立即退出。

下面的示例展示了如何在终端中通过管道向 Gemini CLI 发送命令：

```bash
echo "What is fine tuning?" | gemini
```

Gemini CLI 执行命令并将输出打印到终端。同样地，你也可以使用 `--prompt` 或 `-p` 标志达到同样效果，例如：

```bash
gemini -p "What is fine tuning?"
```