# oblix models

The `oblix models` command displays information about the AI models supported by Oblix for intelligent orchestration.

## Usage

```bash
oblix models [options]
```

## Description

This command shows a comprehensive list of AI models that can be used with Oblix, organized by provider. It displays both local models available through Ollama and supported cloud models from providers like OpenAI and Anthropic.

The command checks your local Ollama installation to show which models you currently have installed, and lists recommended cloud models that work well with Oblix's orchestration capabilities.

## Options

| Option | Description |
|--------|-------------|
| `--refresh` | Force refresh of model list |
| `--help` | Display help for this command |

## Output

The command displays information about models from the following providers:

### Ollama

Shows locally available models served through [Ollama](https://ollama.ai/), including:

- List of currently installed models
- Installation status of Ollama
- Connection status to the Ollama server

If no Ollama models are found or the Ollama server isn't running, it provides instructions for installing and setting up Ollama.

### OpenAI

Lists recommended OpenAI models that work well with Oblix, including:

- GPT-3.5 Turbo
- GPT-4 and variants
- Model capabilities and features
- Context window sizes

### Claude

Lists recommended Claude models from Anthropic, including:

- Claude 3 Opus
- Claude 3 Sonnet
- Claude 3 Haiku
- Model capabilities and features
- Context window sizes

## Example Output

```
Supported Model Providers
Oblix supports the following AI model providers and models:

Ollama
Local models served through Ollama

Installed Ollama models:
  • llama2:latest
  • mistral:latest
  • llama2-uncensored:latest

OpenAI
GPT models via OpenAI API

Recommended OpenAI models:
  • gpt-3.5-turbo - Fast and cost-effective general-purpose model (Context: 16K)
  • gpt-4 - Advanced reasoning capabilities (Context: 8K)
  • gpt-4-turbo - Latest GPT-4 with improved capabilities (Context: 128K)

Claude
Claude models via Anthropic API

Recommended Claude models:
  • claude-3-opus-20240229 - Most capable Claude model for complex tasks (Context: 200K)
  • claude-3-sonnet-20240229 - Balanced performance and speed (Context: 200K)
  • claude-3-haiku-20240307 - Fastest Claude model for simple tasks (Context: 200K)
```

## Using These Models

The models displayed by this command can be used with other Oblix commands:

- With `oblix chat`: Specify them as `--local-model` or `--cloud-model`
- With `oblix server`: Specify them as `--local-model` or `--cloud-model`

For example:
```bash
oblix chat --local-model ollama:llama2:latest --cloud-model openai:gpt-3.5-turbo
```

## Related Commands

- [`oblix chat`](oblix-chat.md) - Start an interactive chat session with these models
- [`oblix server`](oblix-server.md) - Start an API server with these models