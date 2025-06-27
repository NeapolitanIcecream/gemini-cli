# 欢迎来到 Gemini CLI 文档

本文档为安装、使用和开发 Gemini CLI 提供了全面的指南。该工具让您可以通过命令行界面与 Gemini 模型进行交互。

## 概述

Gemini CLI 通过一个交互式的"读取-求值-打印"循环 (REPL) 环境，将 Gemini 模型的强大功能带到您的终端。Gemini CLI 由一个客户端应用程序 (`packages/cli`) 和一个与之通信的本地服务器 (`packages/core`) 组成。本地服务器负责管理对 Gemini API 及其 AI 模型的请求。此外，Gemini CLI 还包含了由 `packages/core` 管理的多种工具，用于执行文件系统操作、运行 shell 和进行网页抓取等任务。

## 文档导航

本文档分为以下几个部分：

- **[执行与部署](./deployment.zh.md):** 运行 Gemini CLI 的相关信息。
- **[架构概述](./architecture.zh.md):** 了解 Gemini CLI 的高层设计，包括其组件及交互方式。
- **CLI 使用:** `packages/cli` 的文档。
  - **[CLI 简介](./cli/index.zh.md):** 命令行界面概述。
  - **[命令](./cli/commands.zh.md):** 可用 CLI 命令的说明。
  - **[配置](./cli/configuration.zh.md):** 关于配置 CLI 的信息。
  - **[检查点](./checkpointing.zh.md):** 检查点功能的文档。
  - **[扩展](./extension.zh.md):** 如何通过新功能扩展 CLI。
  - **[遥测](./telemetry.zh.md):** CLI 中遥测功能的概述。
- **核心详解:** `packages/core` 的文档。
  - **[核心简介](./core/index.zh.md):** 核心组件概述。
  - **[工具 API](./core/tools-api.zh.md):** 关于核心如何管理和暴露工具的信息。
- **工具:**
  - **[工具概述](./tools/index.zh.md):** 可用工具的概述。
  - **[文件系统工具](./tools/file-system.zh.md):** `read_file` 和 `write_file` 工具的文档。
  - **[多文件读取工具](./tools/multi-file.zh.md):** `read_many_files` 工具的文档。
  - **[Shell 工具](./tools/shell.zh.md):** `run_shell_command` 工具的文档。
  - **[网页抓取工具](./tools/web-fetch.zh.md):** `web_fetch` 工具的文档。
  - **[网页搜索工具](./tools/web-search.zh.md):** `google_web_search` 工具的文档。
  - **[内存工具](./tools/memory.zh.md):** `save_memory` 工具的文档。
- **[贡献与开发指南](../CONTRIBUTING.md):** 为贡献者和开发者提供的信息，包括设置、构建、测试和编码规范。
- **[故障排除指南](./troubleshooting.zh.md):** 寻找常见问题的解决方案和常见问题解答。
- **[服务条款和隐私声明](./tos-privacy.zh.md):** 关于您使用 Gemini CLI 时适用的服务条款和隐私声明的信息。

我们希望这份文档能帮助您充分利用 Gemini CLI！ 