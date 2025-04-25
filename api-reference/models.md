# Models

This page provides information about how to use different model types with the Oblix server.

## Model Format

When using the Oblix server, models are specified in the format `provider:model_name`, for example:

- `ollama:llama2` - Refers to the "llama2" model running on Ollama locally
- `openai:gpt-3.5-turbo` - Refers to OpenAI's GPT-3.5 Turbo model
- `claude:claude-3-sonnet-20240229` - Refers to Anthropic's Claude 3 Sonnet model
- `auto` - Special identifier that enables Oblix's intelligent orchestration

## The "auto" Model

The most powerful way to use Oblix is with the special `auto` model, which enables Oblix's intelligent orchestration system:

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:62549/v1",
    api_key="placeholder"  # Required by OpenAI client but not used by Oblix
)

# Using auto model for intelligent orchestration
response = client.chat.completions.create(
    model="auto",  # This enables Oblix's orchestration
    messages=[{"role": "user", "content": "Explain quantum computing"}]
)
```

When using `auto`, Oblix will:
1. Evaluate system resource availability 
2. Check network connectivity status
3. Select the optimal model based on these factors
4. Fall back gracefully between cloud and edge as conditions change

## Using Specific Models

While the `auto` model is recommended for most use cases, you can directly specify models to bypass orchestration and use a specific provider:

```python
# Force execution on a specific local model
response = client.chat.completions.create(
    model="ollama:llama2",  # Specify exact model to bypass orchestration
    messages=[{"role": "user", "content": "Hello!"}]
)

# Force execution on a specific cloud model
response = client.chat.completions.create(
    model="openai:gpt-3.5-turbo",  # Use this model specifically
    messages=[{"role": "user", "content": "Hello!"}]
)
```

## How Models Are Connected

Behind the scenes, the Oblix server connects to:
- Local Ollama models via the Ollama API (typically at http://localhost:11434)
- OpenAI models via the OpenAI API
- Claude models via the Anthropic API

API keys for cloud providers are specified when starting the server:
```bash
oblix server \
  --local-model ollama:llama2 \
  --cloud-model openai:gpt-3.5-turbo \
  --cloud-api-key YOUR_API_KEY
```

or via environment variables:
```bash
export OPENAI_API_KEY=YOUR_API_KEY
export ANTHROPIC_API_KEY=YOUR_API_KEY

oblix server \
  --local-model ollama:llama2 \
  --cloud-model openai:gpt-3.5-turbo
```

## Model Parameters

When making API requests, you can specify model parameters using the same format as the OpenAI API:

```python
response = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "Write a short poem"}],
    temperature=0.7,
    max_tokens=150,
    top_p=1.0,
    frequency_penalty=0.0,
    presence_penalty=0.0
)
```

These parameters will be passed to the underlying model as appropriate.