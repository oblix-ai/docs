# OLLAMA

Ollama models are local models that run directly on your device without requiring internet connectivity.

## Configuration Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `model_name` | Yes | - | Name of the Ollama model (e.g., "llama2", "mistral") |
| `endpoint` | No | "http://localhost:11434" | API endpoint for the Ollama server |

## Hooking an Ollama Model

```python
# Hook a local Ollama model with default endpoint
await client.hook_model(
    model_type=ModelType.OLLAMA,
    model_name="llama2"
)

# Hook with custom endpoint
await client.hook_model(
    model_type=ModelType.OLLAMA,
    model_name="mixtral",
    endpoint="http://192.168.1.100:11434"  # Custom Ollama server
)
```

## Supported Models

Ollama supports various open-source models, including:

- `llama2` - Meta's Llama 2 models
- `mixtral` - Mistral AI's Mixtral models
- `mistral` - Mistral AI's base models
- `phi` - Microsoft's Phi models
- `gemma` - Google's Gemma models
- And many others available through Ollama

## Performance Considerations

Ollama models:
- Run locally on your device, using your system's CPU and/or GPU
- Do not require internet connectivity
- Offer lower latency compared to cloud models
- May have higher resource usage on your local system