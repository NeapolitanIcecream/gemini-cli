# Gemini CLI 的执行与部署

本文档介绍了如何运行 Gemini CLI，并解释了其使用的部署架构。

## 运行 Gemini CLI

有多种方式可以运行 Gemini CLI。具体选择哪种方式取决于您的使用目的。

---

### 1. 标准安装 (推荐给普通用户)

这是推荐给最终用户的安装方式。它会从 NPM 仓库下载 Gemini CLI 软件包。

- **全局安装:**

  ```bash
  # 全局安装 CLI
  npm install -g @google/gemini-cli

  # 现在您可以从任何地方运行 CLI
  gemini
  ```

- **NPX 执行:**
  ```bash
  # 无需全局安装，直接从 NPM 执行最新版本
  npx @google/gemini-cli
  ```

---

### 2. 在沙盒中运行 (Docker/Podman)

为了安全和隔离，Gemini CLI 可以在容器内运行。这也是 CLI 执行可能产生副作用的工具时的默认方式。

- **直接从镜像仓库运行:**
  您可以直接运行已发布的沙盒镜像。这对于只有 Docker 环境又想运行 CLI 的情况非常有用。
  ```bash
  # 运行已发布的沙盒镜像
  docker run --rm -it us-docker.pkg.dev/gemini-code-dev/gemini-cli/sandbox:0.1.1
  ```
- **使用 `--sandbox` 标志:**
  如果您已在本地安装了 Gemini CLI (通过上述的标准安装方式)，您可以指示它在沙盒容器内运行。
  ```bash
  gemini --sandbox "在这里输入你的提示"
  ```

---

### 3. 从源码运行 (推荐给 Gemini CLI 贡献者)

项目贡献者可能希望直接从源码运行 CLI。

- **开发模式:**
  此方法提供热重载功能，非常适合活跃的开发工作。
  ```bash
  # 在仓库根目录运行
  npm run start
  ```
- **类生产模式 (链接的软件包):**
  此方法通过链接本地的软件包来模拟全局安装。这对于在生产工作流中测试本地构建版本很有用。

  ```bash
  # 将本地的 cli 软件包链接到您的全局 node_modules
  npm link packages/cli

  # 现在您可以使用 `gemini` 命令运行您的本地版本
  gemini
  ```

---

### 4. 运行 GitHub 上最新的 Gemini CLI commit

您可以直接从 GitHub 仓库运行最新提交的 Gemini CLI 版本。这对于测试仍在开发中的功能非常有用。

```bash
# 直接从 GitHub 的 main 分支执行 CLI
npx https://github.com/google-gemini/gemini-cli
```

## 部署架构

上述的执行方法得益于以下的架构组件和流程：

**NPM 软件包**

Gemini CLI 项目是一个 monorepo，它向 NPM 仓库发布了两个核心软件包：

- `@google/gemini-cli-core`: 后端，处理逻辑和工具执行。
- `@google/gemini-cli`: 面向用户的界面。

当进行标准安装或从源码运行 Gemini CLI 时，会使用这些软件包。

**构建和打包流程**

根据分发渠道的不同，我们使用两种不同的构建流程：

- **NPM 发布:** 为了发布到 NPM 仓库，`@google/gemini-cli-core` 和 `@google/gemini-cli` 中的 TypeScript 源码会通过 TypeScript 编译器 (`tsc`) 转译为标准的 JavaScript。生成的 `dist/` 目录就是发布到 NPM 软件包中的内容。这是 TypeScript 库的标准做法。

- **GitHub `npx` 执行:** 当直接从 GitHub 运行最新版本的 Gemini CLI 时，`package.json` 中的 `prepare` 脚本会触发一个不同的流程。该脚本使用 `esbuild` 将整个应用及其依赖项打包成一个独立的、自包含的 JavaScript 文件。这个打包文件是在用户机器上即时创建的，并不会提交到仓库中。

**Docker 沙盒镜像**

基于 Docker 的执行方式由 `gemini-cli-sandbox` 容器镜像支持。该镜像被发布到容器仓库，并包含一个预安装的、全局版本的 Gemini CLI。`scripts/prepare-cli-packagejson.js` 脚本会在发布前将该镜像的 URI 动态注入到 CLI 的 `package.json` 中，这样当使用 `--sandbox` 标志时，CLI 就知道该拉取哪个镜像。

## 发布流程

一个统一的脚本 `npm run publish:release` 负责协调整个发布流程。该脚本会执行以下操作：

1.  使用 `tsc` 构建 NPM 软件包。
2.  用 Docker 镜像的 URI 更新 CLI 的 `package.json`。
3.  构建并标记 `gemini-cli-sandbox` Docker 镜像。
4.  将 Docker 镜像推送到容器仓库。
5.  将 NPM 软件包发布到制品仓库。 