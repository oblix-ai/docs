# Sessions

Sessions in Oblix are persistent containers for conversation history that maintain context across multiple interactions with AI models. This API reference covers the session management capabilities provided by the Oblix SDK.

## Basic Session Methods

### Create Session

Create a new session with optional metadata.

```python
async def create_session(
    title: str = None,             # Optional session title
    initial_context: dict = None,  # Optional initial context
    metadata: dict = None          # Optional metadata for categorization
) -> str:                          # Returns session ID string
```

Example:
```python
# Create a simple session
session_id = await client.create_session()

# Create a session with title and metadata
session_id = await client.create_session(
    title="Customer Support Chat",
    initial_context={"customer_id": "cust_123"},
    metadata={"category": "support", "priority": "high"}
)
```

### Create and Use Session

Create a new session and set it as the current active session.

```python
async def create_and_use_session(
    title: str = None,             # Optional session title
    initial_context: dict = None,  # Optional initial context
    metadata: dict = None          # Optional metadata for categorization
) -> str:                          # Returns session ID string (already set as current)
```

Example:
```python
# Create and set as active session in one call
session_id = await client.create_and_use_session(
    title="Customer Support Chat",
    metadata={"category": "support"}
)
```

### Use Session

Set an existing session as the current active session.

```python
def use_session(
    session_id: str                # Session ID to set as active
) -> bool:                         # True if successful
```

Example:
```python
# Set the active session
success = client.use_session(session_id)
```

### Load Session

Load a session by ID and retrieve its data.

```python
def load_session(
    session_id: str                # ID of session to load
) -> dict:                         # Session data dictionary
```

Response structure:
```python
{
    "id": str,                     # Unique session ID
    "title": str,                  # Session title
    "created_at": str,             # ISO format timestamp
    "updated_at": str,             # ISO format timestamp
    "messages": List[dict],        # Message history list
    "context": dict,               # Additional context
    "metadata": dict               # Session metadata
}
```

Example:
```python
# Load an existing session
session_data = client.load_session(session_id)
print(f"Session title: {session_data['title']}")
print(f"Message count: {len(session_data['messages'])}")
```

### List Sessions

List available sessions with optional filtering.

```python
def list_sessions(
    limit: int = 50,               # Maximum number of sessions to return
    filter_metadata: dict = None   # Optional metadata filter
) -> List[dict]:                   # List of session summary dictionaries
```

Example:
```python
# List recent sessions
sessions = client.list_sessions(limit=10)
for session in sessions:
    print(f"ID: {session['id']} | Title: {session['title']}")

# Filter sessions by metadata
support_sessions = client.list_sessions(
    filter_metadata={"category": "support", "priority": "high"}
)
```

### Delete Session

Delete a session by ID.

```python
def delete_session(
    session_id: str                # ID of session to delete
) -> bool:                         # True if successful
```

Example:
```python
# Delete a session
success = client.delete_session(session_id)
if success:
    print(f"Session {session_id} deleted")
```

## Using Sessions with Execute

The `execute` method can be used with sessions to maintain conversation context across multiple interactions. Here are examples of different ways to use sessions with `execute`:

### Single Message in Session

```python
# Create a session first
session_id = await client.create_session("Technical Support Chat")

# Send a message in this session
response = await client.execute(
    prompt="I'm having trouble installing the software",
    session_id=session_id
)
print(f"AI: {response['response']}")

# Continue the conversation in the same session
response = await client.execute(
    prompt="I'm using Windows 11",
    session_id=session_id
)
print(f"AI: {response['response']}")
```

### Interactive Chat with Session

```python
# Start an interactive chat session with streaming
result = await client.execute(
    prompt="I need help with installation problems",
    chat=True,  # Enables interactive chat mode
    stream=True  # Streams the responses (default)
)
# The chat session ID is returned when the chat ends
print(f"Chat ended. Session ID: {result['session_id']}")
```

### Resume Existing Session with Chat

```python
# Resume an existing session with interactive chat
result = await client.execute(
    prompt="Let's continue our previous discussion",
    session_id=existing_session_id,
    chat=True  # Enables interactive chat mode
)
```

## Advanced Session Management

### Update Session Metadata

Update metadata for an existing session.

```python
def update_session_metadata(
    session_id: str,               # ID of session to update
    metadata: dict                 # New metadata to apply/merge
) -> bool:                         # True if successful
```

Example:
```python
# Update session metadata
client.update_session_metadata(
    session_id,
    {"status": "resolved", "resolution_time": "2023-07-15T15:30:00Z"}
)
```

### Get Session Metadata

Get just the metadata for a session without loading the full session.

```python
def get_session_metadata(
    session_id: str                # ID of session to query
) -> dict:                         # Session metadata
```

Example:
```python
# Get just the metadata
metadata = client.get_session_metadata(session_id)
if metadata.get("status") == "resolved":
    print(f"Resolution time: {metadata.get('resolution_time')}")
```

### Export Session

Export a session to a file.

```python
async def export_session(
    session_id: str,               # ID of session to export
    file_path: str                 # File path to export to
) -> bool:                         # True if successful
```

Example:
```python
# Export session to a file
await client.export_session(session_id, "/path/to/export/session.json")
```

### Import Session

Import a session from a file.

```python
async def import_session(
    file_path: str,                # File path to import from
    new_id: bool = False,          # Whether to assign a new ID
    use_immediately: bool = False  # Whether to set as current session
) -> str:                          # Session ID (new or original)
```

Example:
```python
# Import a session from a file
imported_id = await client.import_session(
    "/path/to/import/session.json",
    new_id=True,
    use_immediately=True
)
```

### Copy Session

Create a copy of an existing session.

```python
async def copy_session(
    session_id: str,               # ID of session to copy
    new_title: str = None,         # Optional new title for the copy
    use_immediately: bool = False  # Whether to set as current session
) -> str:                          # New session ID
```

Example:
```python
# Create a copy of a session
copy_id = await client.copy_session(
    session_id,
    new_title="Copy - Customer Onboarding Template",
    use_immediately=True
)
```

### Merge Sessions

Combine multiple sessions into a new session.

```python
async def merge_sessions(
    session_ids: List[str],        # List of session IDs to merge
    title: str = None,             # Optional title for merged session
    use_immediately: bool = False  # Whether to set as current session
) -> str:                          # New merged session ID
```

Example:
```python
# Merge multiple sessions
merged_id = await client.merge_sessions(
    [session_id_1, session_id_2, session_id_3],
    title="Combined Support Case",
    use_immediately=True
)
```

## Session Structure

Each session contains:

- **ID**: Unique identifier for the session
- **Title**: Human-readable title for the session
- **Created/Updated**: Timestamps for creation and last update
- **Messages**: Array of message objects with:
  - **Role**: 'user' or 'assistant'
  - **Content**: Message text
  - **Timestamp**: When the message was added
- **Context**: Optional dictionary for additional context
- **Metadata**: Optional dictionary for categorization and filtering

Example session structure:

```json
{
  "id": "5f3e9a7b-6c1d-4d8e-9e7a-9b8c7d6e5f4a",
  "title": "Technical Support Chat",
  "created_at": "2023-07-15T14:30:22.123Z",
  "updated_at": "2023-07-15T14:35:46.789Z",
  "messages": [
    {
      "id": "msg_1",
      "role": "user",
      "content": "I'm having trouble installing the software.",
      "timestamp": "2023-07-15T14:30:22.123Z"
    },
    {
      "id": "msg_2",
      "role": "assistant",
      "content": "I'm sorry to hear that. What operating system are you using?",
      "timestamp": "2023-07-15T14:30:25.456Z"
    },
    {
      "id": "msg_3",
      "role": "user",
      "content": "I'm using Windows 11.",
      "timestamp": "2023-07-15T14:35:46.789Z"
    }
  ],
  "context": {
    "user_id": "user_789",
    "product": "Oblix SDK"
  },
  "metadata": {
    "category": "technical_support",
    "priority": "medium",
    "status": "active"
  }
}
```

## Complete Example

```python
import asyncio
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
        api_key="your_openai_api_key"
    )
    
    # Add monitoring agents
    client.hook_agent(ResourceMonitor())
    client.hook_agent(ConnectivityAgent())
    
    # Create a new session with metadata
    session_id = await client.create_session(
        title="Product Demo Session",
        metadata={"category": "demo", "customer": "Acme Inc"}
    )
    client.use_session(session_id)
    
    try:
        # Send messages in the session
        print("Starting conversation...")
        
        # First message with session context
        response = await client.execute(
            prompt="Tell me about Oblix's features",
            session_id=session_id,
            stream=True  # Stream the response (default)
        )
        print(f"\nResponse from {response['model_id']}")
        
        # Follow-up message (uses session context)
        response = await client.execute(
            prompt="How does orchestration work?",
            session_id=session_id
        )
        
        # Start an interactive chat continuing this conversation
        print("\nStarting interactive chat...")
        result = await client.execute(
            prompt="Tell me more about the connectivity features",
            session_id=session_id,
            chat=True  # Enable interactive chat mode
        )
        print(f"Chat ended. Session ID: {result['session_id']}")
        
        # Export the session
        await client.export_session(session_id, "demo_session.json")
        print(f"\nSession exported to demo_session.json")
        
    finally:
        # Clean up resources
        await client.shutdown()

if __name__ == "__main__":
    asyncio.run(main())
```