# Agents

Agents in Oblix are monitoring components that provide system awareness and make intelligent orchestration decisions. They analyze system resources, network connectivity, and other factors to help the SDK choose the optimal model for execution.

## Agent Lifecycle

Each agent in the Oblix system follows this lifecycle:

1. **Initialization**: Configure the agent with thresholds and settings
2. **Registration**: Hook the agent to the OblixClient
3. **Monitoring**: Ongoing collection of metrics
4. **Policy Evaluation**: Assessment of metrics against policies
5. **Recommendation**: Providing execution target recommendations

## Available Agents

Oblix provides two main types of monitoring agents for orchestration:

- [Resource Monitor](resource-monitor.md): Monitors system resources including CPU, memory, and GPU utilization.
- [Connectivity](connectivity.md): Monitors network connectivity, including latency, packet loss, and bandwidth.

## Orchestration Process

When working together, agents form a comprehensive orchestration system:

1. **Resource Assessment**: The ResourceMonitor assesses whether local execution is viable
2. **Connectivity Assessment**: The ConnectivityAgent determines if cloud models are accessible
3. **Conflict Resolution**: When recommendations conflict, Oblix uses these priority rules:
   - If resources are critically constrained but connectivity is good, prioritize resource constraints
   - If connectivity is poor but resources are available, prioritize connectivity constraints
   - In balanced scenarios, use policy-defined preferences

## Using Agents

Agents are added to the Oblix client with the `hook_agent()` method:

```python
from oblix import OblixClient
from oblix.agents import ResourceMonitor, ConnectivityAgent

# Initialize client
client = OblixClient()

# Add monitoring agents
client.hook_agent(ResourceMonitor())
client.hook_agent(ConnectivityAgent())
```

Once agents are hooked, the Oblix client automatically consults them during execution to make intelligent routing decisions.

## Agent Checks

When you execute a prompt using the `execute()` method, Oblix runs checks with all registered agents. The results of these checks are included in the response under the `agent_checks` key:

```python
response = await client.execute("Explain quantum computing")

# Access agent check results
agent_checks = response["agent_checks"]
```

The `agent_checks` dictionary contains information about:

- Current resource state
- Connectivity state
- Recommended execution target
- Detailed metrics
- Reasoning for the recommendation

## Accessing Agent Metrics Directly

You can also access the current metrics from agents directly:

```python
# Get resource metrics
resource_metrics = await client.get_resource_metrics()
print(f"CPU usage: {resource_metrics['cpu_percent']}%")

# Access GPU metrics on macOS
if resource_metrics.get('gpu') and resource_metrics['gpu'].get('available'):
    gpu_info = resource_metrics['gpu']
    if gpu_info.get('utilization') is not None:
        print(f"GPU utilization: {gpu_info['utilization'] * 100:.2f}%")
    if gpu_info.get('memory_utilization') is not None:
        print(f"GPU memory utilization: {gpu_info['memory_utilization'] * 100:.2f}%")
    print(f"GPU name: {gpu_info.get('name', 'Unknown')}")

# Get connectivity metrics
connectivity_metrics = await client.get_connectivity_metrics()
print(f"Current latency: {connectivity_metrics['latency']}ms")
```

This direct access is useful for monitoring and debugging orchestration behavior.