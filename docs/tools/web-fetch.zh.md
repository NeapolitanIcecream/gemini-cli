# Web 抓取工具 (`web_fetch`)

本文档描述了 Gemini CLI 的 `web_fetch` 工具。

## 描述

使用 `web_fetch` 来总结、比较或从网页中提取信息。`web_fetch` 工具处理来自嵌入在提示中的一个或多个 URL（最多 20 个）的内容。`web_fetch` 接受自然语言提示并返回生成的响应。

### 参数

`web_fetch` 接受一个参数：

- `prompt` (字符串, 必需): 一个全面的提示，包括要抓取的 URL（最多 20 个）以及如何处理其内容的具体说明。例如：`"总结 https://example.com/article 并从 https://another.com/data 提取关键点"`。提示必须至少包含一个以 `http://` 或 `https://` 开头的 URL。

## 如何通过 Gemini CLI 使用 `web_fetch`

要将 `web_fetch` 与 Gemini CLI 配合使用，请提供包含 URL 的自然语言提示。该工具在抓取任何 URL 之前会请求确认。一旦确认，该工具将通过 Gemini API 的 `urlContext` 处理 URL。

如果 Gemini API 无法访问该 URL，该工具将回退到直接从本地计算机抓取内容。该工具将格式化响应，在可能的情况下包括来源归属和引文。然后，该工具将向用户提供响应。

用法:

```
web_fetch(prompt="您的提示，包括一个 URL，例如 https://google.com。")
```

## `web_fetch` 示例

总结单篇文章：

```
web_fetch(prompt="您能总结一下 https://example.com/news/latest 的要点吗")
```

比较两篇文章：

```
web_fetch(prompt="这两篇论文的结论有何不同：https://arxiv.org/abs/2401.0001 和 https://arxiv.org/abs/2401.0002?")
```

## 重要说明

- **URL 处理:** `web_fetch` 依赖于 Gemini API 访问和处理给定 URL 的能力。
- **输出质量:** 输出的质量将取决于提示中说明的清晰度。
