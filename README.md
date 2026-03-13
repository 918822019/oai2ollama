# Oai2Ollama

这是一个 CLI 工具，用于启动一个服务器，将兼容 OpenAI 的 API 封装为兼容 Ollama 的 API。适用于那些不支持自定义 OpenAI API 但支持 Ollama 的编程助手（例如 VS Code 中的 GitHub Copilot）。

## 使用方式

### 本地运行（推荐）

本仓库是在原项目基础上进行了定制修改的版本。请克隆本仓库并在本地运行，以确保使用的是本仓库中的代码，**而不是 PyPI 上发布的版本**（PyPI 版本不包含本仓库的改动）。

```sh
git clone https://github.com/918822019/oai2ollama.git
cd oai2ollama
uv sync
uv run oai2ollama --help
```

```text
usage: oai2ollama [--api-key str] [--base-url HttpUrl] [--capabilities list[str]] [--models list[str]] [--host str]
options:
  --help, -h                    显示帮助信息并退出
  --api-key str                 用于身份验证的 API 密钥（必填）
  --base-url HttpUrl            兼容 OpenAI 的 API 基础 URL（必填）
  --capabilities, -c list[str]  为模型标记额外支持的能力
  --models, -m list[str]        在 /api/tags 响应中添加额外的模型
  --host str                    API 服务器的 IP / 主机名（默认：localhost）
```

> [!TIP]
> 使用 `--capabilities`（或 `-c`）选项可以为模型标记支持的能力，支持以下两种语法：
>
> `oai2ollama -c tools` 或 `oai2ollama --capabilities tools`
>
> `oai2ollama -c tools -c vision` 或 `oai2ollama --capabilities -c tools,vision`
>
> 若需要支持 `/models` 接口未返回的模型，可使用 `--models`（或 `-m`）选项将其加入 `/api/tags` 响应：
>
> `oai2ollama -m model1 -m model2` 或 `oai2ollama -m model1,model2`
>
> Ollama 目前[支持的能力](https://github.com/ollama/ollama/blob/main/types/model/capability.go#L6-L11)有：
> `tools`、`insert`、`vision`、`embedding`、`thinking` 和 `completion`。`completion` 始终包含在内。

也可以使用 `.env` 文件来设置这些选项：

```properties
OPENAI_API_KEY=your_api_key
OPENAI_BASE_URL=your_base_url
HOST=0.0.0.0
CAPABILITIES=["vision","thinking"]
MODELS=["custom-model1","custom-model2"]
```

> [!WARNING]
> 选项名称 `capacities` 已弃用，请使用 `capabilities` 替代。旧名称目前仍可使用，但会输出弃用警告。

### 使用 Docker

先构建镜像：

```sh
docker build -t oai2ollama .
```

然后使用你的凭据运行容器：

```sh
docker run -p 11434:11434 \
  -e OPENAI_API_KEY="your_api_key" \
  -e OPENAI_BASE_URL="your_base_url" \
  oai2ollama
```

也可以通过命令行参数传入：

```sh
docker run -p 11434:11434 oai2ollama --api-key your_api_key --base-url your_base_url
```

如需监听其他主机（例如所有 IPv6 接口），使用 `--host` 参数：

```sh
docker run -p 11434:11434 oai2ollama --host "::"
```

## 功能特性

- 兼容 Ollama 的 `/api/tags`、`/api/show` 和 `/api/version` 接口
- 兼容 OpenAI 的 `/v1/chat/completions` 和 `/v1/models` 接口（透传）
- 支持流式响应
- 可配置额外模型列表
- 可自定义模型能力（`tools`、`insert`、`vision`、`embedding`、`thinking`）
