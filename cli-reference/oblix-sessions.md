# oblix sessions

The `oblix sessions` command provides tools for viewing and managing your chat history in Oblix.

## Usage

```bash
oblix sessions <subcommand> [options]
```

## Description

This command group allows you to work with your saved chat sessions from previous interactions with Oblix. It provides functionality for listing, viewing, creating, and deleting chat sessions.

Oblix automatically saves your conversation history from chat sessions, and this command helps you manage and access that history.

## Subcommands

| Subcommand | Description |
|------------|-------------|
| `list` | List recent chat sessions |
| `view` | View details of a specific session |
| `delete` | Delete a specific chat session |
| `create` | Create a new chat session |

## List Sessions

Lists recent chat sessions with their IDs, titles, creation timestamps, and message counts.

```bash
oblix sessions list \
  [--limit NUMBER]
```

### Options

| Option | Description |
|--------|-------------|
| `--limit` | Maximum number of sessions to display (default: 50) |

### Example Output

```
Recent Chat Sessions
Session ID                           | Title                      | Created           | Messages
8c7f1e9a-8b4d-4f56-9a9d-6b5c9d8e7f6a | Quantum Computing Explained | 2023-05-10 14:30 | 12
7d9e8c6b-7a3e-5d45-8b8c-7a9b8c7d6e5d | Poetry Writing              | 2023-05-09 10:15 | 8
```

## View Session

Displays the full conversation history for a specific session.

```bash
oblix sessions view <session_id>
```

### Arguments

| Argument | Description |
|----------|-------------|
| `session_id` | Unique identifier of the session to view |

### Example Output

```
┌─Session Overview──────────────────────────┐
│ Session ID: 8c7f1e9a-8b4d-4f56-9a9d-6b5c9d8e7f6a │
│ Title: Quantum Computing Explained          │
│ Created: 2023-05-10T14:30:45.123456         │
│ Last Updated: 2023-05-10T15:10:22.654321    │
└────────────────────────────────────────────┘

Conversation:
User (2023-05-10 14:30:45): Can you explain quantum computing in simple terms?
Assistant (2023-05-10 14:30:52): Quantum computing is a type of computing that uses quantum phenomena...
```

## Delete Session

Deletes a specific chat session permanently.

```bash
oblix sessions delete <session_id>
```

### Arguments

| Argument | Description |
|----------|-------------|
| `session_id` | Unique identifier of the session to delete |

### Safety Confirmation

The command prompts for confirmation before permanently deleting a session:

```
Are you sure you want to delete this session? [y/N]:
```

## Create Session

Creates a new empty chat session with an optional title.

```bash
oblix sessions create \
  [--title "Session Title"]
```

### Options

| Option | Description |
|--------|-------------|
| `--title` | Optional title for the new session |

### Example Output

```
New session created: 9d8c7b6a-5e4d-3f2c-1a9b-8c7d6e5f4g3h
Title: My Custom Session
```

## Sessions Storage

Chat sessions are stored locally on your machine in a JSON format. The storage location is:

```
~/.oblix/sessions/
```

Each session is saved as a separate JSON file with the session ID as the filename.

## Related Commands

- [`oblix chat`](oblix-chat.md) - Start an interactive chat session that creates new session history