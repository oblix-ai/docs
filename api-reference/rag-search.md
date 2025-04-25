# RAG Search

Oblix provides powerful Retrieval-Augmented Generation (RAG) capabilities through its document and directory connectors. This feature allows you to retrieve relevant information from your documents and use it to ground AI responses in factual content.

## Overview

The document connector system in Oblix enables:

- Processing and embedding of individual documents
- Processing entire directories of documents recursively
- Semantic search across your document collection
- Document-grounded chat conversations

This creates a powerful RAG system that enhances AI responses with content from your own documents, making responses more accurate and relevant to your specific data.

## Connector Types

Oblix supports two primary connector types for document processing:

| Connector Type | Description |
|----------------|-------------|
| `document` | Processes a single document file (PDF, TXT, MD, DOCX, etc.) |
| `directory` | Recursively processes all supported documents in a directory |

## Supported Document Types

- PDF files (requires PyPDF2)
- Plain text files (.txt)
- Markdown files (.md)
- Microsoft Word documents (.docx) (requires python-docx)
- CSV, JSON, YAML, and other text formats

## API Usage

### Registering a Document Connector

To process a single document:

```python
import asyncio
from oblix import OblixClient

async def process_document():
    client = OblixClient()
    
    # Register a document connector
    result = await client.register_connector(
        connector_type="document",
        path="/path/to/your/document.pdf",
        embedding_model="text-embedding-3-small",  # OpenAI embedding model (can also use "text-embedding-3-large" for higher quality)
        embedding_api_key="your-openai-api-key",
        alias="research-paper"  # Optional friendly name
    )
    
    if result.get("success", False):
        workspace_id = result.get("workspace_id")
        print(f"Document processed successfully. Workspace ID: {workspace_id}")
    else:
        print(f"Failed to process document: {result.get('error', 'Unknown error')}")

asyncio.run(process_document())
```

### Registering a Directory Connector

To process an entire directory of documents:

```python
import asyncio
from oblix import OblixClient

async def process_directory():
    client = OblixClient()
    
    # Register a directory connector
    result = await client.register_connector(
        connector_type="directory",
        path="/path/to/your/document/directory",
        embedding_model="nomic-embed-text",  # You can use Nomic as well
        embedding_api_key=None,  # Not needed for Nomic
        alias="project-docs",
        recursive=True  # Process subdirectories
    )
    
    if result.get("success", False):
        workspace_id = result.get("workspace_id")
        stats = result.get("stats", {})
        print(f"Directory processed successfully. Workspace ID: {workspace_id}")
        print(f"Processed {stats.get('processed_files', 0)} of {stats.get('total_files', 0)} files")
    else:
        print(f"Failed to process directory: {result.get('error', 'Unknown error')}")

asyncio.run(process_directory())
```

### Executing a RAG Query

After registering a connector, you can use it in your queries. The proper sequence for setting up a RAG query is:

```python
import asyncio
from oblix import OblixClient, ModelType
from oblix.agents import ResourceMonitor, ConnectivityAgent

async def main():
    client = OblixClient()
    
    # 1. First, hook models for orchestration
    await client.hook_model(
        model_type=ModelType.OLLAMA,
        model_name="llama2:latest"
    )
    
    await client.hook_model(
        model_type=ModelType.CLAUDE,
        model_name="claude-3-haiku-20240307",
        api_key="your-anthropic-api-key"
    )
    
    # 2. Hook monitoring agents for intelligent orchestration
    client.hook_agent(ResourceMonitor())
    client.hook_agent(ConnectivityAgent())
    
    # 3. Create a session (optional but recommended for RAG)
    session_id = await client.create_session(title="Document RAG Session")
    
    # 4. Process the document to create embeddings
    result = await client.register_connector(
        connector_type="document",
        path="/path/to/your/document.pdf",
        embedding_model="text-embedding-3-small",
        embedding_api_key="your-openai-api-key",
        alias="research-paper"
    )
    
    # Get the workspace_id from the result
    workspace_id = result.get("workspace_id")
    
    if not workspace_id:
        print("Failed to process document")
        return
    
    # 5. Execute a query with document context
    # The connector API will automatically fetch relevant document sections from the workspace
    response = await client.execute(
        prompt="What are the key findings in the research paper?",
        session_id=session_id,
        workspace_id=workspace_id,  # Pass the workspace_id to use the processed document
        stream=True  # Use streaming for responsive UI
    )
    
    # Print the response
    if response.get("stream", False):
        # For streaming responses
        async for chunk in response.get("stream_iterator"):
            print(chunk.get("content", ""), end="", flush=True)
        print()
    else:
        # For non-streaming responses
        print(response.get("response"))

asyncio.run(main())
```

## Register Connector Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `connector_type` | string | Yes | Type of connector: "document" or "directory" |
| `path` | string | Yes | Path to the document or directory |
| `embedding_model` | string | Yes | Embedding model to use (e.g., "text-embedding-3-small", "nomic-embed-text") |
| `embedding_api_key` | string | No | API key for the embedding model (required for OpenAI models) |
| `alias` | string | No | Friendly name for the document or directory |
| `recursive` | boolean | No | For directory connectors, whether to process subdirectories |

## Response Format

The `register_connector` method returns a dictionary with the following fields:

| Field | Type | Description |
|-------|------|-------------|
| `success` | boolean | Whether the operation was successful |
| `workspace_id` | string | ID of the created workspace (used for future queries) |
| `error` | string | Error message if the operation failed |
| `stats` | object | For directory connectors, statistics about processed files |

## Command Line Interface

These RAG capabilities are also available in the Oblix CLI:

```bash
# Chat with a single document
oblix chat \
  --local-model ollama:llama2 \
  --cloud-model claude:claude-3-haiku \
  --document /path/to/your/document.pdf

# Chat with a directory of documents
oblix chat \
  --local-model ollama:llama2 \
  --cloud-model openai:gpt-4 \
  --directory /path/to/your/docs
```

## Document Processing Flow

1. **Document Extraction**: Text is extracted from the document using format-specific processors
2. **Chunking**: Documents are split into manageable chunks with overlap for context
3. **Embedding**: Each chunk is embedded using the specified embedding model
4. **Storage**: Embeddings are stored in a vector database
5. **Retrieval**: When you query, relevant chunks are retrieved via semantic search
6. **Context Augmentation**: The AI response is grounded in the retrieved document chunks

## Embedding Models

Oblix supports multiple embedding model providers:

| Provider | Model Example | API Key Required |
|----------|---------------|-----------------|
| OpenAI | "text-embedding-3-small", "text-embedding-3-large", "text-embedding-ada-002" (legacy) | Yes |
| Nomic | "nomic-embed-text" | No |

For OpenAI embedding models, you can use the same API key as your OpenAI completion models.

## Best Practices

- Use smaller chunks (400-800 tokens) for precise retrieval
- Include sufficient chunk overlap (100-200 tokens) to maintain context
- For large document collections, use high-quality embedding models
- Structure questions to be clear and specific
- When possible, refer to specific sections or topics in your documents