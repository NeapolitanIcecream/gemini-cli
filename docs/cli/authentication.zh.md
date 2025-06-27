## 身份验证设置

Gemini CLI 需要你先通过 Google 的 AI 服务进行身份验证。首次启动时，你需要配置以下**任意一种**身份验证方式：

1. **使用 Google 登录（Gemini Code Assist）：**

   - 使用此选项通过你的 Google 账号登录。
   - 第一次启动期间，Gemini CLI 会引导你打开网页进行身份验证。认证完成后，凭据会被缓存在本地，后续运行可跳过网页登录。
   - 请注意，进行网页登录的浏览器必须能够与运行 Gemini CLI 的机器通信。（具体来说，浏览器会被重定向到 Gemini CLI 正在监听的本地 `localhost` URL。）
   - <a id="workspace-gca">若出现以下情况，你可能需要指定 `GOOGLE_CLOUD_PROJECT`：</a>
     1. 你使用的是 Google Workspace 账户。Google Workspace 是 Google 面向企业和机构提供的付费生产力套件服务，提供包括自定义电子邮件域名（例如 your-name@your-company.com）、增强的安全功能和管理控件在内的一系列工具，通常由雇主或学校管理。
     2. 你是 Code Assist 的授权用户。可能是你之前购买了 Code Assist 许可，或通过 Google Developer Program 获取了名额。
     - 如果符合上述任一情况，则必须首先配置一个 Google Cloud 项目 ID，[启用 Gemini for Cloud API](https://cloud.google.com/gemini/docs/discover/set-up-gemini#enable-api) 并[配置访问权限](https://cloud.google.com/gemini/docs/discover/set-up-gemini#grant-iam)。你可以在当前 shell 会话中临时设置环境变量：
       ```bash
       export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"
       ```
       - 为了长期使用，你可以将该环境变量添加到 `.env` 文件（位于项目目录或用户主目录）或 shell 的配置文件（如 `~/.bashrc`、`~/.zshrc` 或 `~/.profile`）。例如，以下命令可将环境变量添加到 `~/.bashrc` 文件中：
       ```bash
       echo 'export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"' >> ~/.bashrc
       source ~/.bashrc
       ```

2. **<a id="gemini-api-key"></a>Gemini API 密钥：**

   - 在 Google AI Studio 获取你的 API 密钥：[https://aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)
   - 设置 `GEMINI_API_KEY` 环境变量。以下方法中，将 `YOUR_GEMINI_API_KEY` 替换为你获得的 API 密钥：
     - 你可以在当前 shell 会话中临时设置该变量：
       ```bash
       export GEMINI_API_KEY="YOUR_GEMINI_API_KEY"
       ```
     - 为了长期使用，你可以将该变量添加到 `.env` 文件（位于项目目录或用户主目录）或 shell 的配置文件（如 `~/.bashrc`、`~/.zshrc` 或 `~/.profile`）。例如，以下命令可将环境变量添加到 `~/.bashrc` 文件中：
       ```bash
       echo 'export GEMINI_API_KEY="YOUR_GEMINI_API_KEY"' >> ~/.bashrc
       source ~/.bashrc
       ```

3. **Vertex AI：**
   - 如果未使用 express 模式：
     - 确保你拥有 Google Cloud 项目并已启用 Vertex AI API。
     - 使用以下命令设置 Application Default Credentials (ADC)：
       ```bash
       gcloud auth application-default login
       ```
       详细信息见 [为 Google Cloud 设置 Application Default Credentials](https://cloud.google.com/docs/authentication/provide-credentials-adc)。
     - 设置 `GOOGLE_CLOUD_PROJECT`、`GOOGLE_CLOUD_LOCATION` 和 `GOOGLE_GENAI_USE_VERTEXAI` 环境变量。替换为你的项目 ID 和位置：
       ```bash
       export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"
       export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION" # 例如 us-central1
       export GOOGLE_GENAI_USE_VERTEXAI=true
       ```
     - 为长期使用，可将其写入 `.env` 或 shell 配置文件：
       ```bash
       echo 'export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"' >> ~/.bashrc
       echo 'export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION"' >> ~/.bashrc
       echo 'export GOOGLE_GENAI_USE_VERTEXAI=true' >> ~/.bashrc
       source ~/.bashrc
       ```
   - 如果使用 express 模式：
     - 设置 `GOOGLE_API_KEY` 环境变量，将 `YOUR_GOOGLE_API_KEY` 替换为 express 模式提供的 Vertex AI API 密钥：
       ```bash
       export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"
       export GOOGLE_GENAI_USE_VERTEXAI=true
       ```
     - 相同方式写入 `.env` 或 shell 配置文件：
       ```bash
       echo 'export GOOGLE_API_KEY="YOUR_GOOGLE_API_KEY"' >> ~/.bashrc
       echo 'export GOOGLE_GENAI_USE_VERTEXAI=true' >> ~/.bashrc
       source ~/.bashrc
       ```
