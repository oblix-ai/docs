# oblix chat

The `oblix chat` command starts an interactive AI chat session in your terminal with intelligent orchestration between local and cloud models. It also supports chatting with documents and directories.

## Usage

```bash
oblix chat \
  --local-model <provider:model_name> \
  --cloud-model <provider:model_name> \
  [--document <file_path>] \
  [--directory <directory_path>] \
  [options]
```

## Description

This command launches an interactive chat session where you can have conversations with AI models. Oblix will automatically orchestrate between the specified local and cloud models based on:

- System resource availability
- Network connectivity
- Query complexity

When you run this command, Oblix initializes both models and sets up monitoring agents that continuously assess system conditions. As you chat, Oblix intelligently routes each message to the optimal model.

You can also chat with documents or directories, allowing the AI to reference specific content when answering your questions.

## Required Arguments

| Argument | Description |
|----------|-------------|
| `--local-model` | Local model to use in format `provider:model_name` (e.g., `ollama:llama2:latest`) |
| `--cloud-model` | Cloud model to use in format `provider:model_name` (e.g., `openai:gpt-3.5-turbo` or `claude:claude-3-sonnet-20240229`) |

## Options

| Option | Description |
|--------|-------------|
| `--cloud-api-key` | API key for cloud model (if not set via environment variable) |
| `--document` | Path to a single document to chat with (PDF, TXT, MD, DOCX, etc.) |
| `--directory` | Path to a directory of documents to chat with |
| `--show-metrics` | Display performance metrics after each response |
| `--help` | Display help for this command |

## Environment Variables

| Variable | Description |
|----------|-------------|
| `OPENAI_API_KEY` | Your OpenAI API key (used when the cloud model is from OpenAI) |
| `ANTHROPIC_API_KEY` | Your Anthropic API key (used when the cloud model is from Claude) |

## Examples

### Basic usage with OpenAI

```bash
oblix chat \
  --local-model ollama:llama2:latest \
  --cloud-model openai:gpt-3.5-turbo
```

### Using with Claude

```bash
oblix chat \
  --local-model ollama:llama2:latest \
  --cloud-model claude:claude-3-haiku-20240307
```

### Providing API key directly

```bash
oblix chat \
  --local-model ollama:llama2:latest \
  --cloud-model openai:gpt-3.5-turbo \
  --cloud-api-key sk-your-openai-key
```

### Chat with a specific document

```bash
oblix chat \
  --local-model ollama:llama2:latest \
  --cloud-model claude:claude-3-haiku-20240307 \
  --document ~/Documents/research-paper.pdf
```

### Chat with a directory of documents

```bash
oblix chat \
  --local-model ollama:llama2:latest \
  --cloud-model openai:gpt-3.5-turbo \
  --directory ~/project/docs
```

## Document Chat Features

When you use the `--document` or `--directory` options:

1. Oblix processes the document(s) and creates embeddings for efficient retrieval
2. For directories, Oblix recursively processes supported file types (PDFs, text files, Markdown, etc.)
3. When you ask questions, Oblix searches for the most relevant document sections
4. The AI responds based on both its knowledge and the specific document content
5. The document context follows through the entire conversation

Supported document types:
- PDF files (requires PyPDF2)
- Plain text files (.txt)
- Markdown files (.md)
- Microsoft Word documents (.docx) (requires python-docx)
- CSV, JSON, YAML, and other text formats

## Orchestration in Action

During the chat session, Oblix continuously monitors:

1. **System resources**: CPU, memory, and GPU usage
2. **Network connectivity**: Connection quality and latency
3. **Request complexity**: Determining if the local model can handle the query

Based on these factors, Oblix automatically selects the optimal model for each query. For example:

- If your Mac has limited resources available, Oblix might route to the cloud model
- If you're working offline, Oblix will automatically use the local model
- If the prompt is simple, Oblix might prefer the faster local model

## Interactive Chat Features

When in the chat session:

- Type your message and press Enter to send
- Type 'exit' to end the conversation
- Your conversation history is saved and can be accessed via `oblix sessions` commands

## Related Commands

- [`oblix models`](oblix-models.md) - View available models
- [`oblix sessions`](oblix-sessions.md) - Manage your chat history
- [`oblix agents`](oblix-agents.md) - Learn about the monitoring agents