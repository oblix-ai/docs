# REST API

This page provides an overview of the HTTP REST API endpoints provided by the Oblix Server, particularly focusing on the OpenAI-compatible interface.

## Overview

Oblix Server provides several REST API endpoints for integration with external applications, including a fully compatible OpenAI API interface that allows you to use Oblix with any client library or tool that supports the OpenAI API.

## Base URL

The default Oblix server base URL for all API endpoints is:

```
http://localhost:62549/v1
```

## Client Configuration

To connect to the Oblix server, configure your OpenAI client with the Oblix base URL:

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:62549/v1",
    api_key="placeholder"  # Required by OpenAI client but not used by Oblix
)
```

Note that while Oblix doesn't require its own API key, you will need to provide the appropriate API keys for cloud providers (OpenAI, Claude, etc.) when hooking these models for orchestration.

## Available Endpoints

Oblix provides the following REST API endpoints:

| Endpoint | Method | Description |
|----------|--------|-------------|
| [`/v1/chat/completions`](chat-completions) | POST | Generate chat completions with intelligent orchestration |
| [`/health`](#health-check) | GET | Check server health and version |

## Orchestration with "auto"

The key feature of Oblix's API is the "auto" model selection, which enables intelligent orchestration between local and cloud models:

```python
# Using auto model for intelligent orchestration
response = client.chat.completions.create(
    model="auto",  # This enables Oblix's orchestration
    messages=[{"role": "user", "content": "Explain quantum computing"}]
)
```

When using "auto", Oblix will:
1. Evaluate system resource availability
2. Check network connectivity status
3. Select the optimal model based on these factors
4. Fall back gracefully between cloud and edge as conditions change

## Best Practices

### Connection Management

For high-traffic applications, maintain persistent connections to the Oblix server to avoid connection establishment overhead.

### Error Handling

Implement robust error handling to manage potential issues:

```python
try:
    response = client.chat.completions.create(
        model="auto",
        messages=[{"role": "user", "content": "Hello, world!"}]
    )
except Exception as e:
    # Handle the error appropriately
    print(f"Error communicating with Oblix server: {e}")
```

### Model Selection

While "auto" is recommended for most use cases, you can directly specify models when needed:

```python
# Force execution on a specific model
response = client.chat.completions.create(
    model="ollama:llama2",  # Specify exact model to bypass orchestration
    messages=[{"role": "user", "content": "Hello!"}]
)
```


## API Reference

For detailed documentation on specific endpoints, see:

- [Chat Completions API](chat-completions)
- [Health Check API](health)
- [Using Models](models)