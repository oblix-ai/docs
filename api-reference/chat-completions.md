# Chat Completions

The Chat Completions API is the main endpoint for generating AI responses with Oblix's intelligent orchestration.

## Endpoint: `/v1/chat/completions`

This endpoint is equivalent to OpenAI's Chat Completions API and is used to generate responses based on conversation history.

**Method:** POST

**Request Body:**

```json
{
  "model": "auto",
  "messages": [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What are the three laws of robotics?"}
  ],
  "temperature": 0.7,
  "top_p": 1.0,
  "n": 1,
  "stream": false,
  "max_tokens": null,
  "presence_penalty": 0.0,
  "frequency_penalty": 0.0,
  "logit_bias": null,
  "user": null
}
```

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `model` | string | - | **Always use "auto"** for Oblix's intelligent orchestration to select the best model based on connectivity, system resources, and the specific request |
| `messages` | array | - | An array of message objects representing the conversation history |
| `temperature` | float | 0.7 | Controls randomness: Lowering results in less random completions. Range: 0.0 to 2.0 |
| `top_p` | float | 1.0 | Controls diversity via nucleus sampling: 0.5 means half of all likelihood-weighted options are considered |
| `n` | integer | 1 | Number of chat completion choices to generate for each input message |
| `stream` | boolean | false | If true, partial message deltas will be sent |
| `max_tokens` | integer | null | The maximum number of tokens to generate in the chat completion |
| `presence_penalty` | float | 0.0 | Number between -2.0 and 2.0. Positive values penalize new tokens based on whether they appear in the text so far |
| `frequency_penalty` | float | 0.0 | Number between -2.0 and 2.0. Positive values penalize new tokens based on their existing frequency in the text so far |
| `logit_bias` | object | null | Modify the likelihood of specified tokens appearing in the completion |
| `user` | string | null | A unique identifier representing your end-user, which can help Oblix monitor and detect abuse |

## Response Structure

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "created": 1677858242,
  "model": "auto (selected: ollama:llama2)",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "The Three Laws of Robotics as formulated by Isaac Asimov are:\n\n1. A robot may not injure a human being or, through inaction, allow a human being to come to harm.\n\n2. A robot must obey the orders given it by human beings except where such orders would conflict with the First Law.\n\n3. A robot must protect its own existence as long as such protection does not conflict with the First or Second Law."
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 30,
    "completion_tokens": 97,
    "total_tokens": 127
  }
}
```

## Streaming Mode

Oblix supports streaming responses by setting the `stream` parameter to `true`. This is now the default behavior in the Python SDK. In streaming mode, the API will return a stream of events as the response is being generated.

**Example Python Code with Streaming:**

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:62549/v1",
    api_key="placeholder"  # Required by OpenAI client but not used by Oblix
)

stream = client.chat.completions.create(
    model="auto",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What is quantum computing?"}
    ],
    stream=True,
    temperature=0.7
)

for chunk in stream:
    if chunk.choices[0].delta.content is not None:
        print(chunk.choices[0].delta.content, end="")
```

**Using the Oblix Python SDK with Streaming (Default):**

```python
from oblix import OblixClient

client = OblixClient()
# Configure models...

# Stream is enabled by default (stream=True)
response = await client.execute(
    prompt="What is quantum computing?",
    temperature=0.7
)
# Response is streamed to the console automatically
```

## Example: Basic Usage

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:62549/v1",
    api_key="placeholder"  # Required by OpenAI client but not used by Oblix
)

response = client.chat.completions.create(
    model="auto",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What is the capital of France?"}
    ],
    temperature=0.7
)

print(response.choices[0].message.content)
```

**Using the Oblix Python SDK:**

```python
from oblix import OblixClient

client = OblixClient()
# Configure models...

# Non-streaming execution with response as return value
response = await client.execute(
    prompt="What is the capital of France?",
    temperature=0.7,
    stream=False  # Disable streaming to get complete response at once
)
print(response["response"])
```

## Example: Setting Different Temperatures

```python
# For more deterministic responses
response = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "Write a poem about autumn."}],
    temperature=0.2  # Lower temperature for more focused, deterministic output
)

# For more creative responses
response = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "Write a poem about autumn."}],
    temperature=1.2  # Higher temperature for more diverse, creative output
)
```

**Using the Oblix Python SDK:**

```python
# For more deterministic responses
response = await client.execute(
    prompt="Write a poem about autumn.",
    temperature=0.2  # Lower temperature for more focused output
)

# For more creative responses
response = await client.execute(
    prompt="Write a poem about autumn.",
    temperature=1.2  # Higher temperature for more diverse output
)
```

## Example: Limiting Response Length

```python
response = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "Explain the history of computing."}],
    max_tokens=100  # Limit response to approximately 100 tokens
)
```

**Using the Oblix Python SDK:**

```python
response = await client.execute(
    prompt="Explain the history of computing.",
    max_tokens=100  # Limit response to approximately 100 tokens
)
```

## Example: Chat Mode with Sessions

The Oblix Python SDK supports an interactive chat mode that helps maintain conversation context:

```python
# Start an interactive chat session
result = await client.execute(
    prompt="Let's discuss quantum computing.",
    chat=True,  # Enable interactive chat mode
    stream=True  # Stream responses (default)
)

# The chat session ID is returned after chat ends
session_id = result["session_id"]

# You can also use an existing session
result = await client.execute(
    prompt="I have more questions about quantum computing.",
    session_id=existing_session_id,
    chat=True  # Start interactive chat using this session
)
```

## Example: Adjusting Creativity with Top-p

```python
response = client.chat.completions.create(
    model="auto",
    messages=[{"role": "user", "content": "Generate a story idea."}],
    top_p=0.5  # Only consider the top 50% of probability mass for each token
)
```