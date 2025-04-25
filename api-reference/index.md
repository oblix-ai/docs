# API Reference

This section provides detailed documentation of the Oblix SDK API for orchestrating AI models between cloud and edge environments.

## Overview

Oblix provides a simple, unified API to orchestrate AI models across different providers (OpenAI, Claude, Ollama) with intelligent routing based on system resources and connectivity. The API is designed to be intuitive and developer-friendly while providing powerful orchestration capabilities.

## Main Components

### [Client](client.md)

The main entry point to the SDK. Handles model management, orchestration, execution routing, and session management.

```python
from oblix import OblixClient

# Initialize the client
client = OblixClient()
```

### [ModelType](model-types.md)

Enum that defines the supported model types/providers for orchestration.

```python
from oblix import ModelType

# Available model types for orchestration
ModelType.OLLAMA  # Local edge models via Ollama
ModelType.OPENAI  # Cloud models from OpenAI (GPT series)
ModelType.CLAUDE  # Cloud models from Anthropic (Claude series)
```

### [Agents](agents.md)

Monitoring agents that provide system awareness for orchestration between cloud and edge.

```python
from oblix.agents import ResourceMonitor, ConnectivityAgent

# Add system monitoring for orchestration
client.hook_agent(ResourceMonitor())
client.hook_agent(ConnectivityAgent())
```

### [Sessions](sessions.md)

Manage persistent chat sessions with context awareness.

```python
# Create a new session
session_id = await client.create_session()

# Continue an existing session
session_data = await client.load_session(session_id)
```

### [RAG Search](rag-search.md)

Document and directory connectors for retrieval-augmented generation (RAG).

```python
# Register a document connector
result = await client.register_connector(
    connector_type="document",
    path="/path/to/document.pdf",
    embedding_model="text-embedding-3-small"
)

# Use documents in your queries
response = await client.execute("What does the document say about...")
```

### [REST API](rest-api.md)

HTTP API endpoints for integrating with Oblix Server, including the OpenAI-compatible interface.

```python
from openai import OpenAI

# Connect to Oblix Server using OpenAI-compatible endpoints
client = OpenAI(base_url="http://localhost:62549/v1", api_key="any-value")
```

## Orchestration Workflow

Using the Oblix API for orchestration typically follows this pattern:

1. **Initialize client**
2. **Hook models** (at least one local edge model and one cloud model)
3. **Add monitoring agents** for orchestration
4. **Execute prompts** with automatic orchestration between cloud and edge

```python
import asyncio
import os
from oblix import OblixClient, ModelType
from oblix.agents import ResourceMonitor, ConnectivityAgent

async def main():
    # 1. Initialize client
    client = OblixClient()
    
    # 2. Hook models for orchestration
    await client.hook_model(
        model_type=ModelType.OLLAMA,
        model_name="llama2:latest"
    )
    
    await client.hook_model(
        model_type=ModelType.OPENAI, 
        model_name="gpt-3.5-turbo", 
        api_key=os.getenv('OPENAI_API_KEY')
    )
    
    # 3. Add monitoring agents for orchestration
    client.hook_agent(ResourceMonitor())    # Monitors local resources
    client.hook_agent(ConnectivityAgent())  # Monitors network connectivity
    
    # 4. Execute a prompt with automatic orchestration
    response = await client.execute("Explain quantum computing in simple terms")
    print(f"Response from {response['model_id']}: {response['response']}")
    
    # 5. Access orchestration decision data
    agent_checks = response["agent_checks"]
    print(f"Resource state: {agent_checks.get('resource_monitor', {}).get('state')}")
    print(f"Connectivity: {agent_checks.get('connectivity_agent', {}).get('state')}")

# Run the example
if __name__ == "__main__":
    asyncio.run(main())
```

## Orchestration Benefits

Oblix's API provides several key benefits for AI orchestration:

1. **Automatic model selection** based on current device and network conditions
2. **Seamless fallback** between cloud and edge models
3. **Optimized performance** by balancing local computation and cloud capabilities
4. **Cost efficiency** by using local models when possible
5. **Resilience** against connectivity issues or resource constraints

## Next Steps

Explore detailed documentation for each component:

- [Client API Reference](client.md)
- [ModelType Enum Reference](model-types.md)
- [Agents Reference](agents.md)
- [Sessions Reference](sessions.md)
- [RAG Search Reference](rag-search.md)
- [REST API Reference](rest-api.md)