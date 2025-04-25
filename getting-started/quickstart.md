# Quickstart Guide

Get started with Oblix, an AI orchestration SDK for seamless switching between local and cloud models based on connectivity and system resources.

## Installation

Install Oblix using pip:

```bash
pip install oblix
```

You can also install for a specific Python version:

```bash
python3.11 -m pip install oblix
```

**Note:** Oblix is currently available for macOS and Linux (x86) platforms. Windows support is planned for future releases. Supported Python versions: 3.9, 3.10, 3.11, 3.12, 3.13.

## Prerequisites

Before using Oblix with local models, you'll need to:

1. Install [Ollama](https://ollama.ai/) on your machine
2. Download at least one model using Ollama (e.g., `ollama pull llama2`)

## Basic Usage

### 1. Initialize Client

```python
from oblix import OblixClient

# Initialize the client
client = OblixClient()
```

### 2. Hook Models

Connect at least one local and one cloud model:

```python
import os
from oblix import ModelType

# Hook a local Ollama model (replace "llama2" with any model you've pulled)
await client.hook_model(
    model_type=ModelType.OLLAMA, 
    model_name="llama2:latest"  # Use any available Ollama model
)

# Hook a cloud model (OpenAI)
openai_api_key = os.getenv('OPENAI_API_KEY')
await client.hook_model(
    model_type=ModelType.OPENAI, 
    model_name="gpt-3.5-turbo", 
    api_key=openai_api_key
)
```

### 3. Add Monitoring Agents

```python
from oblix.agents import ResourceMonitor, ConnectivityAgent

# Add resource monitoring
resource_monitor = ResourceMonitor(name="resource_monitor")

# Add connectivity monitoring
connectivity_monitor = ConnectivityAgent(name="connectivity_monitor")

# Register agents with the client
client.hook_agent(resource_monitor)
client.hook_agent(connectivity_monitor)
```

### 4. Execute Prompts

```python
# Execute a prompt - Oblix automatically orchestrates the optimal model
# Streaming is enabled by default (stream=True)
response = await client.execute("Explain quantum computing in simple terms")
print(f"Model used: {response['model_id']}")

# Execute without streaming to get the full response at once
response = await client.execute(
    prompt="What is machine learning?",
    stream=False  # Disable streaming
)
print(response["response"])
```

### 5. Interactive Chat Session

```python
# Start an interactive chat session (streaming enabled by default)
result = await client.execute(
    prompt="Let's discuss artificial intelligence",
    chat=True  # This enables interactive chat mode
)
# Returns the session ID when chat ends
print(f"Chat session ID: {result['session_id']}")

# When finished, clean up resources
await client.shutdown()
```

## Example: Complete Setup

```python
#!/usr/bin/env python3
import asyncio
import os
from dotenv import load_dotenv
from oblix import OblixClient, ModelType
from oblix.agents import ResourceMonitor, ConnectivityAgent

# Load environment variables from .env file
load_dotenv()

async def main():
    # Initialize client without API key
    client = OblixClient()
    
    # Set up resource monitor
    resource_monitor = ResourceMonitor(name="resource_monitor")
    
    # Set up connectivity monitor
    connectivity_monitor = ConnectivityAgent(name="connectivity_monitor")
    
    # Hook models - local Ollama model (replace with any model you've pulled)
    await client.hook_model(
        model_type=ModelType.OLLAMA,
        model_name="llama2:latest"  # Use any available Ollama model
    )
    
    # Hook models - cloud OpenAI model
    openai_api_key = os.getenv('OPENAI_API_KEY')
    if not openai_api_key:
        raise ValueError("OPENAI_API_KEY environment variable must be set")
    
    await client.hook_model(
        model_type=ModelType.OPENAI, 
        model_name="gpt-3.5-turbo", 
        api_key=openai_api_key
    )
    
    # Add monitoring agents (Oblix will use these for orchestration)
    client.hook_agent(resource_monitor)
    client.hook_agent(connectivity_monitor)
    
    # Start interactive chat session
    result = await client.execute(
        prompt="Hello, I'd like to learn about AI orchestration",
        chat=True  # Enables interactive chat mode (streaming enabled by default)
    )
    print(f"Chat ended. Session ID: {result['session_id']}")
    
    # Clean up resources
    await client.shutdown()

# Run the example
if __name__ == "__main__":
    try:
        asyncio.run(main())
    except KeyboardInterrupt:
        print("\nProgram interrupted by user. Exiting...")
    except ValueError as e:
        print(f"\nConfiguration Error: {str(e)}")
        print("Please ensure all required environment variables are set.")
    except Exception as e:
        print(f"\nError: {str(e)}")
        import traceback
        traceback.print_exc()
```

## What's Next?

- Check out the [API reference](../api-reference/client.md)