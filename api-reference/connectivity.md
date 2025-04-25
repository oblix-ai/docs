# Connectivity

The ConnectivityAgent in Oblix monitors network connectivity, including latency, packet loss, and bandwidth to determine if cloud model execution is viable.

## Creating a Connectivity Agent

```python
from oblix.agents import ConnectivityAgent

# Create with default settings
connectivity_agent = ConnectivityAgent()

# Create with custom configuration
connectivity_agent = ConnectivityAgent(
    name="custom_connectivity_monitor",
    latency_threshold=150.0,       # Maximum acceptable latency in ms (default: 200.0)
    packet_loss_threshold=5.0,     # Maximum acceptable packet loss percentage (default: 10.0)
    bandwidth_threshold=10.0,      # Minimum acceptable bandwidth in Mbps (default: 5.0)
    check_interval=60              # Seconds between connectivity checks (default: 30)
)
```

## Connectivity States

The connectivity agent reports one of the following states:

| State | Description |
|-------|-------------|
| `OPTIMAL` | Good connectivity suitable for cloud model use |
| `DEGRADED` | Limited connectivity that may affect performance |
| `DISCONNECTED` | No connectivity, must use local models |

## Connection Targets

Based on connectivity state, the agent recommends one of these targets:

| Target | Description |
|--------|-------------|
| `LOCAL` | Use local models due to connectivity issues |
| `CLOUD` | Use cloud models due to good connectivity |
| `HYBRID` | Consider balancing between local and cloud models |

## Accessing Connectivity Metrics Directly

You can access the current connectivity metrics directly:

```python
# Get connectivity metrics
connectivity_metrics = await client.get_connectivity_metrics()
print(f"Current latency: {connectivity_metrics['latency']}ms")
print(f"Packet loss: {connectivity_metrics['packet_loss']}%")
print(f"Bandwidth: {connectivity_metrics['bandwidth']} Mbps")
```