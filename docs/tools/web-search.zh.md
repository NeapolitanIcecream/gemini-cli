# Web 搜索工具 (`google_web_search`)

本文档介绍 `google_web_search` 工具。

## 描述

使用 `google_web_search` 通过 Gemini API 使用 Google 搜索执行 web 搜索。`google_web_search` 工具返回带有来源的 web 结果摘要。

### 参数

`google_web_search` 接受一个参数：

- `query` (字符串, 必需): 搜索查询。

## 如何通过 Gemini CLI 使用 `google_web_search`

`google_web_search` 工具向 Gemini API 发送一个查询，然后执行 web 搜索。`google_web_search` 将根据搜索结果返回一个生成的响应，包括引文和来源。

用法:

```
google_web_search(query="在此处输入您的查询。")
```

## `google_web_search` 示例

获取有关某个主题的信息：

```
google_web_search(query="人工智能代码生成领域的最新进展")
```

## 重要说明

- **返回的响应：** `google_web_search` 工具返回一个处理过的摘要，而不是原始的搜索结果列表。
- **引文：** 响应包括用于生成摘要的来源的引文。 