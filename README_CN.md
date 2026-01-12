## 为什么

Gemini API 提供了具有*慷慨限额*的[免费](https://ai.google.dev/gemini-api/docs/pricing#free)层级，但仍有许多工具仅支持 OpenAI API。

本项目提供了一个免费的个人 OpenAI 兼容端点。
注：如果不想折腾，可以直接在这个中转站申请：https://www.aicodemirror.com/register?invitecode=49QBC2


## Serverless?

尽管它运行在云端，但无需服务器维护。
它可以轻松地免费部署到各种提供商
（拥有适合个人使用的慷慨限额）。

> [!TIP]
> 在本地运行代理端点也是一个[选项](#本地运行---使用-node-deno-bun)！


## 如何开始

你需要一个个人的 Google [API key](https://aistudio.google.com/app/api-keys)。
当然你也可以在这个中转站申请：https://www.aicodemirror.com/register?invitecode=49QBC2

> [!IMPORTANT]
> 即使你位于[支持区域](https://ai.google.dev/gemini-api/docs/available-regions#available_regions)之外，
> 仍然可以通过 VPN 获取。

使用以下说明将项目部署到其中一个提供商。
你需要在那边注册一个账户。

如果你选择“一键部署”，你将被引导先 fork 仓库，
这是持续集成 (CI) 所必需的。


### 部署到 Vercel

 [![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/PublicAffairs/openai-gemini&repository-name=my-openai-gemini)
- 或者可以使用 [cli](https://vercel.com/docs/cli) 部署：
  `vercel deploy`
- 本地运行：`vercel dev`
- Vercel _Functions_ [限制](https://vercel.com/docs/functions/limitations)（使用 _Edge_ 运行时）


### 部署到 Netlify

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/PublicAffairs/openai-gemini&integrationName=integrationName&integrationSlug=integrationSlug&integrationDescription=integrationDescription)
- 或者可以使用 [cli](https://docs.netlify.com/cli/get-started/) 部署：
  `netlify deploy`
- 本地运行：`netlify dev`
- 提供了两个不同的 API 基础路径：
  - `/v1` (例如 `/v1/chat/completions` 端点)
    _Functions_ [限制](https://docs.netlify.com/build/functions/get-started/#synchronous-function)
  - `/edge/v1`
    _Edge functions_ [限制](https://docs.netlify.com/build/edge-functions/limits/)


### 部署到 Cloudflare

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/PublicAffairs/openai-gemini)
- 或者可以手动部署：将 [`src/worker.mjs`](src/worker.mjs) 的内容粘贴到
  https://workers.cloudflare.com/playground (点击那里的 `Deploy` 按钮)。
- 或者可以使用 [cli](https://developers.cloudflare.com/workers/wrangler/) 部署：
  `wrangler deploy`
- 本地运行：`wrangler dev`
- _Worker_ [限制](https://developers.cloudflare.com/workers/platform/limits/#worker-limits)


### 部署到 Deno

查看详情 [这里](https://github.com/PublicAffairs/openai-gemini/discussions/19)。


### 本地运行 - 使用 Node, Deno, Bun

仅适用于 Node：`npm install`。

然后 `npm run start` / `npm run start:deno` / `npm run start:bun`。


#### 开发模式 (监听源码变更)

仅适用于 Node：`npm install --include=dev`

然后：`npm run dev` / `npm run dev:deno` / `npm run dev:bun`。


## 如何使用
如果你在浏览器中打开新部署的站点，你只会看到 `404 Not Found` 消息。这是预期的，因为 API 不是为直接浏览器访问设计的。
要使用它，你应该在软件设置的相应字段中输入你的 API 地址和 Gemini API 密钥。

> [!NOTE]
> 并非所有软件工具都允许覆盖 OpenAI 端点，但许多都支持
> (然而这些设置有时可能隐藏得很深)。

通常，你应该以此格式指定 API 基础路径 (API base)：
`https://my-super-proxy.vercel.app/v1`

相关字段可能标记为 "_OpenAI proxy_"。
你可能需要在 "_Advanced settings_" 或类似部分下查找。
或者，它可能在某个配置文件中 (查看相关文档以获取详细信息)。

对于某些命令行工具，你可能需要设置环境变量，_例如_：
```sh
OPENAI_BASE_URL="https://my-super-proxy.vercel.app/v1"
```
_..或者_:
```sh
OPENAI_API_BASE="https://my-super-proxy.vercel.app/v1"
```


## 模型

如果名称以 "gemini-"、"gemma-" 或 "models/" 开头，请求将使用指定的 [模型]。
否则，应用以下默认值：

- `chat/completions`: `gemini-flash-latest`
- `embeddings`: `gemini-embedding-001`

[模型]: https://ai.google.dev/gemini-api/docs/models


## 内置工具

要使用 **网络搜索** 工具，请在模型名称后附加 ":search"
(例如，"gemini-2.5-flash:search")。

注意：`annotations` 消息属性未实现。


## 媒体

支持 [Vision] 和 [audio] 输入，符合 OpenAI [规范]。
通过 [`inlineData`](https://ai.google.dev/api/caching#Part) 实现。

[vision]: https://platform.openai.com/docs/guides/images-vision?api-mode=chat&format=url#giving-a-model-images-as-input
[audio]: https://platform.openai.com/docs/guides/audio?example=audio-in&lang=curl#add-audio-to-your-existing-application
[规范]: https://platform.openai.com/docs/api-reference/chat/create


## Gemini 特有功能

Gemini 支持一些 OpenAI 模型中不可用的功能，
可以使用 `extra_body` 字段启用。
其中最值得注意的是 [`thinking_config`](https://ai.google.dev/gemini-api/docs/openai#thinking)。

更多详细信息，请参阅 [Gemini API 文档](https://ai.google.dev/gemini-api/docs/openai#extra-body)。

---

## 支持的 API 端点和适用参数

- [x] `chat/completions`

  目前，大多数适用于两个 API 的参数都已实现。
  <details>

  - [x] `messages`
      - [x] `content`
      - [x] `role`
          - [x] "system" (=>`system_instruction`)
          - [x] "user"
          - [x] "assistant"
          - [x] "tool"
      - [x] `tool_calls`
  - [x] `model`
  - [x] `frequency_penalty`
  - [ ] `logit_bias`
  - [ ] `logprobs`
  - [ ] `top_logprobs`
  - [x] `max_tokens`, `max_completion_tokens`
  - [x] `n` (`candidateCount` <8, 不支持流式)
  - [x] `presence_penalty`
  - [x] `reasoning_effort`
  - [x] `response_format`
      - [x] "json_object"
      - [x] "json_schema" (OpenAPI 3.0 schema 对象的一个精选子集)
      - [x] "text"
  - [x] `seed`
  - [x] `stop`: string|array (`stopSequences` [1,5])
  - [x] `stream`
  - [x] `stream_options`
      - [x] `include_usage`
  - [x] `temperature` (OpenAPI 为 0.0..2.0，但 Gemini 支持到无穷大)
  - [x] `top_p`
  - [x] `tools`
  - [x] `tool_choice`
  - [ ] `parallel_tool_calls` (在 Gemini 中始终处于活动状态)
  - [x] [`extra_body`](#gemini-specific-functions)

  </details>
- [ ] `completions`
- [x] `embeddings`
  - [x] `dimensions`
- [x] `models`
