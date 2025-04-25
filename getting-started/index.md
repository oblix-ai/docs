# Introduction

Welcome to Oblix! This section will guide you through the process of installing the SDK, setting up your environment, and creating your first Oblix application.

## What is Oblix?

Oblix is a comprehensive SDK for intelligent orchestration of AI models between edge and cloud environments. The SDK provides:

- Unified interface for multiple AI model providers (OpenAI, Claude, Ollama)
- Intelligent orchestration that seamlessly switches between edge and cloud models
- System resource monitoring and connectivity awareness through specialized agents
- Persistent chat session management
- Agent system for extensible monitoring and orchestration decision making

## Installation

Oblix is currently available for macOS and Linux (x86) platforms and can be installed using pip:

```bash
pip install oblix
```

For a specific Python version, you can use:

```bash
python3.11 -m pip install oblix
```

Oblix supports Python versions 3.9, 3.10, 3.11, 3.12, and 3.13.

## Prerequisites

Before using Oblix, you'll need:

1. **Supported OS** - Oblix currently supports macOS and Linux (x86) platforms
2. **Provider API Keys** for any cloud models you want to use
3. **Ollama** installed if you want to use local edge models

## Next Steps

To continue getting started with Oblix, check out our comprehensive guide:

- [Quickstart](quickstart.md) - A step-by-step guide to installing and using Oblix

Once you're set up, you can explore the [API reference](../api-reference/index.md) for detailed information about the available functions and classes.