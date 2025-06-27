# Gemini CLI 中的沙盒功能

本文档为 Gemini CLI 中的沙盒功能提供指南，内容包括先决条件、快速入门和配置。

## 先决条件

在使用沙盒功能之前，您需要安装和设置 Gemini CLI：

```bash
# 使用 npm 安装 gemini-cli
npm install -g @google/gemini-cli

# 验证安装
gemini --version
```

## 沙盒概述

沙盒功能将潜在的危险操作（如 shell 命令或文件修改）与您的主机系统隔离开来，在 AI 操作和您的环境之间提供了一道安全屏障。

沙盒的好处包括：

- **安全性**: 防止意外的系统损坏或数据丢失。
- **隔离性**: 将文件系统访问权限限制在项目目录内。
- **一致性**: 确保在不同系统上环境的可复现性。
- **安全性**: 在处理不受信任的代码或实验性命令时降低风险。

## 沙盒方法

您理想的沙盒方法可能会因您的平台和偏好的容器解决方案而有所不同。

### 1. macOS Seatbelt (仅限 macOS)

使用 `sandbox-exec` 的轻量级内置沙盒功能。

**默认配置文件**: `permissive-open` - 限制在项目目录外的写入操作，但允许大多数其他操作。

### 2. 基于容器 (Docker/Podman)

具有完全进程隔离的跨平台沙盒功能。

**注意**: 需要在本地构建沙盒镜像，或使用您组织镜像仓库中已发布的镜像。

## 快速入门

```bash
# 使用命令标志启用沙盒
gemini -s -p "分析代码结构"

# 使用环境变量
export GEMINI_SANDBOX=true
gemini -p "运行测试套件"

# 在 settings.json 中配置
{
  "sandbox": "docker"
}
```

## 配置

### 启用沙盒 (按优先级排序)

1. **命令标志**: `-s` 或 `--sandbox`
2. **环境变量**: `GEMINI_SANDBOX=true|docker|podman|sandbox-exec`
3. **设置文件**: `settings.json` 中的 `"sandbox": true`

### macOS Seatbelt 配置文件

内置配置文件 (通过 `SEATBELT_PROFILE` 环境变量设置):

- `permissive-open` (默认): 有写入限制，允许网络
- `permissive-closed`: 有写入限制，无网络
- `permissive-proxied`: 有写入限制，通过代理访问网络
- `restrictive-open`: 严格限制，允许网络
- `restrictive-closed`: 最严格的限制

## Linux UID/GID 处理

沙盒会自动处理 Linux 上的用户权限。您可以使用以下命令覆盖这些权限：

```bash
export SANDBOX_SET_UID_GID=true   # 强制使用主机的 UID/GID
export SANDBOX_SET_UID_GID=false  # 禁用 UID/GID 映射
```

## 故障排除

### 常见问题

**"Operation not permitted" (操作不允许)**

- 操作需要沙盒外的访问权限。
- 尝试使用更宽松的配置文件或添加挂载点。

**缺少命令**

- 添加到自定义的 Dockerfile 中。
- 通过 `sandbox.bashrc` 安装。

**网络问题**

- 检查沙盒配置文件是否允许网络访问。
- 验证代理配置。

### 调试模式

```bash
DEBUG=1 gemini -s -p "调试命令"
```

### 检查沙盒

```bash
# 检查环境
gemini -s -p "运行 shell 命令: env | grep SANDBOX"

# 列出挂载点
gemini -s -p "运行 shell 命令: mount | grep workspace"
```

## 安全提示

- 沙盒能降低风险，但不能消除所有风险。
- 在满足工作需求的前提下，使用限制最严格的配置文件。
- 首次构建后，容器的开销很小。
- GUI 应用程序可能无法在沙盒中运行。

## 相关文档

- [配置](./cli/configuration.zh.md): 完整的配置选项。
- [命令](./cli/commands.zh.md): 可用的命令。
- [故障排除](../troubleshooting.zh.md): 一般性故障排除。 