# oblix agents

The `oblix agents` command displays information about the monitoring agents that help Oblix make intelligent orchestration decisions.

## Usage

```bash
oblix agents
```

## Description

This command shows details about the specialized monitoring agents that Oblix uses for system awareness and orchestration. These agents continuously monitor system resources and network connectivity to help make smart decisions about which model (local or cloud) to use for any given request.

When you run this command, Oblix provides information about each available agent, its purpose, and how it contributes to the orchestration process.

## Output

The command displays information about the following agents:

### Resource Monitor

The Resource Monitor agent tracks system resources including:

- CPU utilization and load
- Memory availability 
- GPU utilization (if available)
- Process resource consumption

Based on these metrics, the Resource Monitor helps Oblix decide when to use local models versus cloud models. For example, if your system is under heavy load, Oblix might route to cloud models to avoid further taxing your local resources.

### Connectivity Agent

The Connectivity Agent monitors network conditions including:

- Internet connectivity status
- Connection quality and stability
- Latency and packet loss
- Bandwidth availability

This agent enables Oblix to make intelligent decisions about cloud model usage. For example, if connectivity is limited or unavailable, Oblix will automatically route to local models instead.

## Example Output

```
Supported Agents

Oblix supports the following agents for system monitoring and task management:

Resource Monitor
  • Monitors system resources like CPU, memory, and GPU
  • Helps make intelligent routing decisions based on resource availability

Connectivity Agent
  • Monitors network connectivity and latency
  • Routes requests to local models when connectivity is limited
```

## How Agents Work Together

The agents work together to provide a complete picture of the system state:

1. The Resource Monitor evaluates if local execution is feasible given current system load
2. The Connectivity Agent determines if cloud models are accessible and responsive
3. Oblix's orchestration engine combines this information to select the optimal model
4. These decisions happen in real-time for each request

## Related Commands

- [`oblix chat`](oblix-chat.md) - Start an interactive chat session that uses these agents
- [`oblix server`](oblix-server.md) - Start the Oblix API server that uses these agents