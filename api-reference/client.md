# Client API

The `OblixClient` class is the main entry point for the Oblix SDK. It provides a high-level interface for working with multiple AI models, managing sessions, and utilizing intelligent orchestration based on system resources and connectivity.

## Initialization

```python
from oblix import OblixClient

client = OblixClient(
    host: str = "localhost",    # Host address (default: localhost)
    port: int = 4321,           # Port number (default: 4321)
    config_path: str = None     # Path to configuration file (optional)
)
```

> **Note:** Oblix no longer requires an API key for initialization.

## Core Methods

### Hook Model

Register a new AI model with the client for orchestration.

```python
async def hook_model(
    model_type: ModelType,      # Type of model (e.g., ModelType.OLLAMA)
    model_name: str,            # Name of model (e.g., "llama2:latest", "gpt-3.5-turbo") 
    endpoint: str = None,       # API endpoint for Ollama models (optional)
    api_key: str = None,        # API key for cloud models (optional)
    **kwargs                    # Additional model-specific parameters
) -> bool:                      # Returns True if successful
```

Example:
```python
# Hook a local Ollama model
await client.hook_model(
    model_type=ModelType.OLLAMA,
    model_name="llama2:latest"
)

# Hook an OpenAI model
await client.hook_model(
    model_type=ModelType.OPENAI,
    model_name="gpt-3.5-turbo",
    api_key="your_openai_api_key"
)

# Hook a Claude model
await client.hook_model(
    model_type=ModelType.CLAUDE,
    model_name="claude-3-opus-20240229",
    api_key="your_anthropic_api_key"
)
```

### Hook Agent

Register an agent with the client for orchestration monitoring.

```python
def hook_agent(
    agent: BaseAgent           # Agent instance to hook
) -> bool:                     # Returns True if successful
```

Example:
```python
from oblix.agents import ResourceMonitor, ConnectivityAgent

# Add resource monitoring for orchestration
client.hook_agent(ResourceMonitor())

# Add connectivity monitoring for orchestration
client.hook_agent(ConnectivityAgent(
    latency_threshold=150.0,       # Maximum acceptable latency in ms (default: 200.0)
    packet_loss_threshold=5.0,     # Maximum acceptable packet loss percentage (default: 10.0)
    bandwidth_threshold=10.0       # Minimum acceptable bandwidth in Mbps (default: 5.0)
))
```

### Execute

Execute a prompt using available models with intelligent orchestration.

```python
async def execute(
    prompt: str,               # User prompt to process
    temperature: float = None, # Sampling temperature (optional)
    max_tokens: int = None,    # Maximum tokens to generate (optional)
    request_id: str = None,    # Custom request identifier (optional)
    display_metrics: bool = True, # Whether to display performance metrics
    session_id: str = None,    # Session ID to use for this interaction
    stream: bool = True,       # Whether to stream the response token-by-token (default: True)
    chat: bool = False,        # Whether to start an interactive chat loop (default: False)
    **kwargs                   # Additional model-specific parameters
) -> dict:                     # Response dictionary
```

Response structure:
```python
{
    "request_id": str,         # Request identifier
    "model_id": str,           # ID of model used for generation
    "response": str,           # Generated text response
    "metrics": dict,           # Performance metrics
    "routing_decision": dict,  # Orchestration decisions
    "agent_checks": list       # Results from agent checks (list of dicts)
}
```

Example with standard execution:
```python
# Execution with automatic orchestration
response = await client.execute("Explain quantum computing")
print(response["response"])
print(f"Model used by orchestration: {response['model_id']}")
```

Example with streaming (default):
```python
# Stream response with automatic orchestration
response = await client.execute(
    prompt="Explain quantum computing",
    stream=True  # This is the default, so it can be omitted
)
# Tokens are printed to the console in real-time, then final response returned
print(f"Model used by orchestration: {response['model_id']}")
```

Example with chat mode:
```python
# Start an interactive chat after processing the initial prompt
result = await client.execute(
    prompt="Hello, how can you help me today?",
    chat=True,  # This will start an interactive chat loop after the response
    stream=True  # You can control streaming even in chat mode
)
# Returns the session_id when chat ends
print(f"Chat session ID: {result['session_id']}")
```

Example with session:
```python
# First create or get a session
session_id = await client.create_session("My Chat Session")

# Execute with session context
response = await client.execute(
    prompt="Tell me about quantum computing",
    session_id=session_id
)

# Follow-up question using the same session
response = await client.execute(
    prompt="How is it different from classical computing?",
    session_id=session_id
)
```

> **Note:** For Oblix to provide orchestration, you should hook at least one local model (Ollama), one cloud model (OpenAI/Claude), and the appropriate monitoring agents. Orchestration decisions are made automatically based on resource and connectivity policies.

## Session Management Methods

### Create Session

Create a new chat session with optional title and initial context.

```python
async def create_session(
    title: str = None,              # Optional session title
    initial_context: dict = None,   # Optional initial context
    metadata: dict = None           # Optional additional metadata
) -> str:                           # New session ID
```

Example:
```python
# Create a simple session
session_id = await client.create_session("Customer Support Chat")
```

### Create and Use Session

Create a new session and set it as the current active session.

```python
async def create_and_use_session(
    title: str = None,              # Optional session title
    initial_context: dict = None,   # Optional initial context
    metadata: dict = None           # Optional additional metadata
) -> str:                           # New session ID (already set as current)
```

Example:
```python
# Create and activate a session in one call
session_id = await client.create_and_use_session("Technical Support")
```

### Use Session

Set an existing session as the current active session.

```python
def use_session(
    session_id: str                 # Session ID to activate
) -> bool:                          # True if successful
```

Example:
```python
# Set an existing session as active
success = client.use_session(session_id)
```

### List Sessions

List recent chat sessions with metadata and optional filtering.

```python
def list_sessions(
    limit: int = 50,                # Maximum number of sessions to return
    filter_metadata: dict = None    # Optional metadata filters
) -> List[dict]:                    # List of session metadata dictionaries
```

Example:
```python
# List recent sessions
sessions = client.list_sessions(limit=10)
for session in sessions:
    print(f"ID: {session['id']} | Title: {session['title']}")
```

### Load Session

Load a specific chat session by ID.

```python
def load_session(
    session_id: str                 # Session identifier
) -> dict:                          # Session data if found
```

Example:
```python
# Load a session
session_data = client.load_session(session_id)
print(f"Message count: {len(session_data.get('messages', []))}")
```

## Monitoring Methods

### List Models

List all available models grouped by type.

```python
def list_models() -> dict:    # Dictionary mapping model types to lists of model names
```

### Get Model

Get configuration for a specific model.

```python
def get_model(
    model_type: str,          # Type of model (e.g., 'ollama', 'openai', 'claude')
    model_name: str           # Name of model
) -> dict:                    # Model configuration if found
```

### Get Resource Metrics

Get current resource metrics from the resource monitor agent.

```python
async def get_resource_metrics() -> dict:  # Dictionary of resource metrics
```

Example:
```python
# Check resource availability for local model execution
resources = await client.get_resource_metrics()
print(f"CPU usage: {resources['cpu_percent']}%")
```

### Shutdown

Gracefully shut down all models and agents.

```python
async def shutdown() -> None:  # Clean up all resources
```

## Complete Example

Here's a complete example that demonstrates using the Oblix Client with orchestration:

```python
import asyncio
import os
from oblix import OblixClient, ModelType
from oblix.agents import ResourceMonitor, ConnectivityAgent

async def main():
    # Initialize the client
    client = OblixClient()
    
    # Hook models for orchestration
    await client.hook_model(
        model_type=ModelType.OLLAMA,
        model_name="llama2:latest"
    )
    
    await client.hook_model(
        model_type=ModelType.OPENAI, 
        model_name="gpt-3.5-turbo", 
        api_key=os.getenv('OPENAI_API_KEY')
    )
    
    # Add monitoring agents for orchestration
    client.hook_agent(ResourceMonitor())
    client.hook_agent(ConnectivityAgent())
    
    try:
        # Execute a prompt with automatic orchestration
        print("Executing prompt with orchestration...")
        response = await client.execute(
            prompt="Explain quantum computing in simple terms",
            stream=True,  # Stream the response (default)
            display_metrics=True  # Show performance metrics (default)
        )
        print(f"\nResponse from {response['model_id']}")
        
        # Start interactive chat
        print("\nStarting interactive chat (press Ctrl+C to exit)...")
        result = await client.execute(
            prompt="Hello, I'd like to learn more about quantum computing",
            chat=True,  # This will start an interactive chat session
            stream=True  # With streaming responses
        )
        print(f"Chat ended. Session ID: {result['session_id']}")
    finally:
        # Clean up resources
        await client.shutdown()

if __name__ == "__main__":
    asyncio.run(main())
```