# ModelType

The `ModelType` enum defines the supported AI model providers in the Oblix orchestration system. It's used when hooking models to specify which provider the model belongs to.

## Import

```python
from oblix import ModelType
```

## Enum Values

| Enum Value | Description |
|------------|-------------|
| `ModelType.OLLAMA` | Local models served via Ollama |
| `ModelType.OPENAI` | Cloud models provided by OpenAI (GPT series) |
| `ModelType.CLAUDE` | Cloud models provided by Anthropic (Claude series) |

## Orchestration Categories

For orchestration purposes, Oblix groups these model types into two categories:

1. **Local Models**: `OLLAMA` models run locally on the device
2. **Cloud Models**: `OPENAI` and `CLAUDE` models run in the cloud

This distinction is important for orchestration decisions, as Oblix intelligently routes prompts between local and cloud models based on system state.

## Usage

The `ModelType` enum is used primarily with the `hook_model()` method to specify which provider a model belongs to:

```python
from oblix import OblixClient, ModelType

client = OblixClient()

# Hook a local model for orchestration
await client.hook_model(
    model_type=ModelType.OLLAMA,
    model_name="llama2"
)

# Hook a cloud model for orchestration
await client.hook_model(
    model_type=ModelType.OPENAI,
    model_name="gpt-3.5-turbo",
    api_key="your_openai_api_key"
)

# Hook another cloud model for orchestration
await client.hook_model(
    model_type=ModelType.CLAUDE,
    model_name="claude-3-opus-20240229",
    api_key="your_anthropic_api_key"
)
```

## Orchestration Model Selection

When multiple models of different types are hooked, Oblix selects the appropriate model for execution based on:

1. **Resource availability**: CPU, memory, and GPU usage (from ResourceMonitor)
2. **Connectivity status**: Network latency, bandwidth, and reliability (from ConnectivityAgent)
3. **Model capabilities**: Features and performance characteristics of each model

You can override automatic selection by explicitly specifying a model ID in the format `{model_type}:{model_name}` when executing prompts.