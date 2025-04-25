# CLAUDE

Claude models are cloud-based models developed by Anthropic that provide high-quality language capabilities with excellent instruction following.

## Configuration Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `model_name` | Yes | Name of the Claude model (e.g., "claude-3-opus-20240229") |
| `api_key` | Yes | Your Anthropic API key |

## Hooking a Claude Model

```python
# Hook a Claude model
await client.hook_model(
    model_type=ModelType.CLAUDE,
    model_name="claude-3-opus-20240229",
    api_key="your_anthropic_api_key"  # Or use os.getenv('ANTHROPIC_API_KEY')
)
```

## Supported Models

Anthropic offers several Claude models:

- `claude-3-opus-20240229` - Most capable Claude model for complex tasks
- `claude-3-sonnet-20240229` - Balanced performance and efficiency
- `claude-3-haiku-20240307` - Fastest and most efficient model
- And other Claude models available through the Anthropic API

## Performance Considerations

Claude models:
- Run in the cloud, requiring internet connectivity
- Have higher latency compared to local models
- Do not use your system's computational resources
- Support very long context windows (up to 200K tokens on some models)