# oblix server

The `oblix server` command starts an Oblix API server that provides OpenAI-compatible endpoints powered by intelligent orchestration between local and cloud models.

## Usage

```bash
oblix server \
  --local-model <provider:model_name> \
  --cloud-model <provider:model_name> \
  [options]
```

## Description

This command launches an API server that exposes OpenAI-compatible endpoints. The server intelligently orchestrates between the specified local and cloud models based on system resources, connectivity, and request complexity.

When you run this command, Oblix starts a server that can be used with any client library or application that supports the OpenAI API format. This allows you to integrate Oblix's intelligent orchestration capabilities into your existing applications.

## Required Arguments

| Argument | Description |
|----------|-------------|
| `--local-model` | Local model to use in format `provider:model_name` (e.g., `ollama:llama2`) |
| `--cloud-model` | Cloud model to use in format `provider:model_name` (e.g., `openai:gpt-3.5-turbo` or `claude:claude-3-sonnet-20240229`) |

## Options

| Option | Description |
|--------|-------------|
| `--port` | Port to run the server on (default: 62549) |
| `--host` | Host to bind the server to (default: 0.0.0.0) |
| `--cloud-api-key` | API key for cloud model (if not set via environment variable) |
| `--help` | Display help for this command |

## Environment Variables

| Variable | Description |
|----------|-------------|
| `OPENAI_API_KEY` | Your OpenAI API key (used when the cloud model is from OpenAI) |
| `ANTHROPIC_API_KEY` | Your Anthropic API key (used when the cloud model is from Claude) |

## Examples

### Basic usage with OpenAI

```bash
oblix server \
  --local-model ollama:llama2 \
  --cloud-model openai:gpt-3.5-turbo
```

### Using with Claude and custom port

```bash
oblix server \
  --local-model ollama:mistral \
  --cloud-model claude:claude-3-haiku-20240307 \
  --port 8000
```

### Providing API key directly

```bash
oblix server \
  --local-model ollama:llama2 \
  --cloud-model openai:gpt-3.5-turbo \
  --cloud-api-key sk-your-openai-key
```

## Server Endpoints

Once the server is running, the following endpoints are available:

| Endpoint | Description |
|----------|-------------|
| `http://localhost:<port>/v1/chat/completions` | OpenAI-compatible chat completions endpoint |
| `http://localhost:<port>/health` | Server health check endpoint |

## Using with Clients

You can use the server with any OpenAI-compatible client. Here are some examples:

### Python with OpenAI Library

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:62549/v1", api_key="any-value")
response = client.chat.completions.create(
    model="auto",  # Use "auto" for Oblix's intelligent orchestration
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What is AI orchestration?"}
    ]
)
print(response.choices[0].message.content)
```

### JavaScript with Fetch API

```javascript
fetch("http://localhost:62549/v1/chat/completions", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": "Bearer any-value"
  },
  body: JSON.stringify({
    model: "auto",
    messages: [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "What is AI orchestration?"}
    ]
  })
})
.then(response => response.json())
.then(data => console.log(data.choices[0].message.content));
```

## Orchestration with "auto" Model

When using the Oblix server, you should specify `"model": "auto"` in your requests to enable full orchestration capabilities. This allows Oblix to:

1. Dynamically select the optimal model for each request
2. Switch between local and cloud models based on system state
3. Provide seamless fallback if one model becomes unavailable

## Server Output

When you start the server, you'll see:

- Confirmation of the server URL and port
- Information about the OpenAI-compatible endpoint
- Example usage instructions
- Status of the connected models

## Related Commands

- [`oblix models`](oblix-models.md) - View available models
- [`oblix agents`](oblix-agents.md) - Learn about the monitoring agents