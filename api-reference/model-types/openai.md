# OPENAI

OpenAI models are cloud-based models that provide state-of-the-art language capabilities through the OpenAI API.

## Configuration Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `model_name` | Yes | Name of the OpenAI model (e.g., "gpt-4", "gpt-3.5-turbo") |
| `api_key` | Yes | Your OpenAI API key |

## Hooking an OpenAI Model

```python
# Hook an OpenAI model
await client.hook_model(
    model_type=ModelType.OPENAI,
    model_name="gpt-3.5-turbo",
    api_key="your_openai_api_key"  # Or use os.getenv('OPENAI_API_KEY')
)
```

## Supported Models

OpenAI offers several models with different capabilities:

- `gpt-4-turbo` - Most capable GPT-4 model for complex tasks
- `gpt-4` - High-capability model for complex reasoning
- `gpt-3.5-turbo` - Fast and cost-effective for most use cases
- And other GPT models available through the OpenAI API

## Performance Considerations

OpenAI models:
- Run in the cloud, requiring internet connectivity
- Have higher latency compared to local models
- Do not use your system's computational resources
- Provide high-quality responses for complex queries