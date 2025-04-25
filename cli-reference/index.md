# CLI Reference

Oblix provides a command-line interface (CLI) that helps you get started with the Oblix AI Orchestration SDK directly from your terminal. The CLI offers a convenient way to experience Oblix's core orchestration capabilities and understand how it intelligently routes between local and cloud AI models.

> **Note:** While the CLI provides a great way to experience Oblix's capabilities, for full implementation details and advanced features, please refer to the [API Reference](../api-reference/client.md) documentation. The CLI is designed as an introduction to orchestration, whereas the Python API provides complete access to all features.

## Installation

The Oblix CLI is included with the main Oblix SDK package when you install it via pip:

```bash
pip install oblix
```

> **Note:** Currently, Oblix supports macOS and Linux (x86) platforms. Windows support is planned for future releases. Supported Python versions: 3.9, 3.10, 3.11, 3.12, 3.13.

## Basic Usage

Once installed, you can access the CLI by using the `oblix` command in your terminal:

```bash
oblix --help
```

This will display the available commands and options.

## Command Structure

The Oblix CLI follows this general structure:

```bash
oblix [command] [options]
```

## Orchestration Capabilities

The CLI provides a taste of Oblix's orchestration capabilities between cloud and edge environments. Rather than running models in isolation, Oblix's CLI demonstrates:

- **Automatic orchestration** between local and cloud models based on resource availability and connectivity
- **Dynamic routing** of prompts to the most appropriate model
- **Monitoring agents** that continuously assess system state
- **Seamless fallback** between models when conditions change

For developers seeking to fully leverage these capabilities in their applications, we recommend using the Python SDK as documented in the API Reference.

## Available Commands

| Command | Description |
|---------|-------------|
| [`chat`](oblix-chat.md) | Start an interactive AI chat in your terminal |
| [`agents`](oblix-agents.md) | Show the monitoring agents that help Oblix make smart decisions |
| [`models`](oblix-models.md) | Show which AI models Oblix supports |
| [`server`](oblix-server.md) | Start the Oblix API server to use with any client application |
| [`sessions`](oblix-sessions.md) | View and manage your chat history |
| [`check-updates`](oblix-check-updates.md) | Check if a newer version of Oblix is available |

## Authentication

Oblix no longer requires an API key for general usage. However, provider-specific API keys are still needed for cloud models:

1. For OpenAI models, set the `OPENAI_API_KEY` environment variable
2. For Claude models, set the `ANTHROPIC_API_KEY` environment variable

Example:
```bash
export OPENAI_API_KEY="your_openai_api_key"
export ANTHROPIC_API_KEY="your_anthropic_api_key"
```

## Common Options

These options are available for most commands:

| Option | Description |
|--------|-------------|
| `--help`, `-h` | Display help for a command |
| `--version` | Show the Oblix CLI version |

## Environment Variables

| Variable | Description |
|----------|-------------|
| `OPENAI_API_KEY` | Your OpenAI API key (used as default for OpenAI models) |
| `ANTHROPIC_API_KEY` | Your Anthropic API key (used as default for Claude models) |

## Troubleshooting

If you encounter issues with the CLI:

1. Ensure you're using the latest version of Oblix
2. Check that you've properly set up your API keys
3. Verify that the Ollama server is running if using local models
4. Confirm your Mac meets the system requirements (particularly for larger models)
5. Check network connectivity if cloud models aren't responding

For additional help, contact Oblix support at [team@oblix.ai](mailto:team@oblix.ai).