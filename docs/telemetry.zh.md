# Gemini CLI 可观测性指南

遥测（Telemetry）提供了关于 Gemini CLI 性能、健康状况和使用情况的数据。启用遥测后，您可以通过追踪、指标和结构化日志来监控操作、调试问题并优化工具的使用。

Gemini CLI 的遥测系统基于 **[OpenTelemetry] (OTEL)** 标准构建，允许您将数据发送到任何兼容的后端。

[OpenTelemetry]: https://opentelemetry.io/

## 启用遥测

您可以通过多种方式启用遥测。配置主要通过 [`.gemini/settings.json` 文件](./cli/configuration.zh.md) 和环境变量进行管理，但 CLI 标志可以覆盖特定会话的这些设置。

### 优先级顺序

以下列表明了应用遥测设置的优先级，排名越高的项目优先级越高：

1.  **CLI 标志 (用于 `gemini` 命令):**

    - `--telemetry` / `--no-telemetry`: 覆盖 `telemetry.enabled`。
    - `--telemetry-target <local|gcp>`: 覆盖 `telemetry.target`。
    - `--telemetry-otlp-endpoint <URL>`: 覆盖 `telemetry.otlpEndpoint`。
    - `--telemetry-log-prompts` / `--no-telemetry-log-prompts`: 覆盖 `telemetry.logPrompts`。

2.  **环境变量:**

    - `OTEL_EXPORTER_OTLP_ENDPOINT`: 覆盖 `telemetry.otlpEndpoint`。

3.  **工作区设置文件 (`.gemini/settings.json`):** 来自此项目特定文件中 `telemetry` 对象的值。

4.  **用户设置文件 (`~/.gemini/settings.json`):** 来自此全局用户文件中 `telemetry` 对象的值。

5.  **默认值:** 如果以上任何方式都未设置，则应用默认值。
    - `telemetry.enabled`: `false`
    - `telemetry.target`: `local`
    - `telemetry.otlpEndpoint`: `http://localhost:4317`
    - `telemetry.logPrompts`: `true`

**对于 `npm run telemetry -- --target=<gcp|local>` 脚本:**
此脚本的 `--target` 参数*仅*在该脚本的执行期间和特定目的下覆盖 `telemetry.target`（即选择要启动的收集器）。它不会永久更改您的 `settings.json`。该脚本会首先在 `settings.json` 中查找 `telemetry.target` 作为其默认值。

### 示例设置

以下代码可以添加到您的工作区 (`.gemini/settings.json`) 或用户 (`~/.gemini/settings.json`) 设置中，以启用遥测并将输出发送到 Google Cloud：

```json
{
  "telemetry": {
    "enabled": true,
    "target": "gcp"
  },
  "sandbox": false
}
```

## 运行 OTEL 收集器

OTEL 收集器是一项接收、处理和导出遥测数据的服务。
CLI 使用 OTLP/gRPC 协议发送数据。

在 [文档][otel-config-docs] 中了解更多关于 OTEL 导出器标准配置的信息。

[otel-config-docs]: https://opentelemetry.io/docs/languages/sdk-configuration/otlp-exporter/

### 本地

使用 `npm run telemetry -- --target=local` 命令来自动化设置本地遥测管道的过程，包括在您的 `.gemini/settings.json` 文件中配置必要的设置。该脚本会安装 `otelcol-contrib` (OpenTelemetry 收集器) 和 `jaeger` (用于查看追踪的 Jaeger UI)。使用方法如下：

1.  **运行命令**:
    在仓库的根目录执行以下命令：

    ```bash
    npm run telemetry -- --target=local
    ```

    该脚本将会：

    - 在需要时下载 Jaeger 和 OTEL。
    - 启动一个本地 Jaeger 实例。
    - 启动一个配置为从 Gemini CLI 接收数据的 OTEL 收集器。
    - 在您的工作区设置中自动启用遥测。
    - 退出时禁用遥测。

2.  **查看追踪**:
    打开您的网页浏览器并访问 **http://localhost:16686** 来访问 Jaeger UI。在这里您可以检查 Gemini CLI 操作的详细追踪信息。

3.  **检查日志和指标**:
    该脚本会将 OTEL 收集器的输出（包括日志和指标）重定向到 `~/.gemini/tmp/<projectHash>/otel/collector.log`。脚本会提供链接，方便您在本地查看和跟踪遥测数据（追踪、指标、日志）。

4.  **停止服务**:
    在运行脚本的终端中按 `Ctrl+C` 来停止 OTEL 收集器和 Jaeger 服务。

### Google Cloud

使用 `npm run telemetry -- --target=gcp` 命令来自动化设置一个本地 OpenTelemetry 收集器，该收集器会将数据转发到您的 Google Cloud 项目，包括在您的 `.gemini/settings.json` 文件中配置必要的设置。该脚本会安装 `otelcol-contrib`。使用方法如下：

1.  **先决条件**:

    - 拥有一个 Google Cloud 项目 ID。
    - 导出 `GOOGLE_CLOUD_PROJECT` 环境变量，使其对 OTEL 收集器可用。
      ```bash
      export GOOGLE_CLOUD_PROJECT="your-project-id"
      ```
    - 使用 Google Cloud 进行身份验证 (例如，运行 `gcloud auth application-default login` 或确保 `GOOGLE_APPLICATION_CREDENTIALS` 已设置)。
    - 确保您的 Google Cloud 帐户/服务帐户具有必要的 IAM 角色："Cloud Trace Agent"、"Monitoring Metric Writer" 和 "Logs Writer"。

2.  **运行命令**:
    在仓库的根目录执行以下命令：

    ```bash
    npm run telemetry -- --target=gcp
    ```

    该脚本将会：

    - 在需要时下载 `otelcol-contrib` 二进制文件。
    - 启动一个配置为从 Gemini CLI 接收数据并将其导出到您指定的 Google Cloud 项目的 OTEL 收集器。
    - 在您的工作区设置 (`.gemini/settings.json`) 中自动启用遥测并禁用沙盒模式。
    - 提供直接链接以在您的 Google Cloud 控制台中查看追踪、指标和日志。
    - 退出时 (Ctrl+C)，它会尝试恢复您原来的遥测和沙盒设置。

3.  **运行 Gemini CLI:**
    在另一个终端中，运行您的 Gemini CLI 命令。这将生成遥测数据，并由收集器捕获。

4.  **在 Google Cloud 中查看遥测**:
    使用脚本提供的链接导航到 Google Cloud 控制台，查看您的追踪、指标和日志。

5.  **检查本地收集器日志**:
    该脚本会将本地 OTEL 收集器的输出重定向到 `~/.gemini/tmp/<projectHash>/otel/collector-gcp.log`。脚本提供链接，方便您在本地查看和跟踪收集器日志。

6.  **停止服务**:
    在运行脚本的终端中按 `Ctrl+C` 来停止 OTEL 收集器。

## 日志和指标参考

以下部分描述了为 Gemini CLI 生成的日志和指标的结构。

- `sessionId` 作为通用属性包含在所有日志和指标中。

### 日志

日志是特定事件的带时间戳的记录。为 Gemini CLI 记录以下事件：

- `gemini_cli.config`: 此事件在启动时发生一次，记录 CLI 的配置。

  - **属性**:
    - `model` (string)
    - `embedding_model` (string)
    - `sandbox_enabled` (boolean)
    - `core_tools_enabled` (string)
    - `approval_mode` (string)
    - `api_key_enabled` (boolean)
    - `vertex_ai_enabled` (boolean)
    - `code_assist_enabled` (boolean)
    - `log_prompts_enabled` (boolean)
    - `file_filtering_respect_git_ignore` (boolean)
    - `debug_mode` (boolean)
    - `mcp_servers` (string)

- `gemini_cli.user_prompt`: 此事件在用户提交提示时发生。

  - **属性**:
    - `prompt_length`
    - `prompt` (如果 `log_prompts_enabled` 配置为 `false`，则此属性会被排除)

- `gemini_cli.tool_call`: 此事件针对每个函数调用发生。

  - **属性**:
    - `function_name`
    - `function_args`
    - `duration_ms`
    - `success` (boolean)
    - `decision` (string: "accept", "reject", or "modify", 如适用)
    - `error` (如适用)
    - `error_type` (如适用)

- `gemini_cli.api_request`: 此事件在向 Gemini API 发出请求时发生。

  - **属性**:
    - `model`
    - `request_text` (如适用)

- `gemini_cli.api_error`: 如果 API 请求失败，则会发生此事件。

  - **属性**:
    - `model`
    - `error`
    - `error_type`
    - `status_code`
    - `duration_ms`

- `gemini_cli.api_response`: 此事件在收到来自 Gemini API 的响应时发生。

  - **属性**:
    - `model`
    - `status_code`
    - `duration_ms`
    - `error` (可选)
    - `input_token_count`
    - `output_token_count`
    - `cached_content_token_count`
    - `thoughts_token_count`
    - `tool_token_count`
    - `response_text` (如适用)

### 指标

指标是行为随时间变化的数值度量。为 Gemini CLI 收集以下指标：

- `gemini_cli.session.count` (计数器, 整数): 每个 CLI 启动时增加一次。

- `gemini_cli.tool.call.count` (计数器, 整数): 统计工具调用次数。

  - **属性**:
    - `function_name`
    - `success` (boolean)
    - `decision` (string: "accept", "reject", or "modify", 如适用)

- `gemini_cli.tool.call.latency` (直方图, 毫秒): 测量工具调用延迟。

  - **属性**:
    - `function_name`
    - `decision` (string: "accept", "reject", or "modify", 如适用)

- `gemini_cli.api.request.count` (计数器, 整数): 统计所有 API 请求次数。

  - **属性**:
    - `model`
    - `status_code`
    - `error_type` (如适用)

- `gemini_cli.api.request.latency` (直方图, 毫秒): 测量 API 请求延迟。

  - **属性**:
    - `model`

- `gemini_cli.token.usage` (计数器, 整数): 统计使用的令牌数量。

  - **属性**:
    - `model`
    - `type` (string: "input", "output", "thought", "cache", or "tool")

- `gemini_cli.file.operation.count` (计数器, 整数): 统计文件操作次数。

  - **属性**:
    - `operation` (string: "create", "read", "update"): 文件操作的类型。
    - `lines` (整数, 如适用): 文件中的行数。
    - `mimetype` (string, 如适用): 文件的 Mimetype。
    - `extension` (string, 如适用): 文件的扩展名。

</rewritten_file> 