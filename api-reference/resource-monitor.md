# Resource Monitor

The ResourceMonitor agent in Oblix tracks system resources including CPU, memory, and GPU utilization to determine if local model execution is feasible. On macOS, it can collect detailed GPU metrics including real-time utilization and memory usage without requiring sudo privileges.

## Creating a Resource Monitor

```python
from oblix.agents import ResourceMonitor

# Create with default settings
resource_monitor = ResourceMonitor()

# Create with custom thresholds
resource_monitor = ResourceMonitor(
    name="custom_resource_monitor",
    custom_thresholds={
        "cpu_threshold": 70.0,     # CPU usage percentage (default: 80.0)
        "memory_threshold": 75.0,  # RAM usage percentage (default: 85.0)
        "load_threshold": 3.0,     # System load average (default: 4.0)
        "gpu_threshold": 75.0,     # GPU utilization percentage (default: 85.0)
        "critical_gpu": 90.0       # Critical GPU threshold (default: 95.0)
    }
)
```

## Resource States

The resource monitor reports one of the following states:

| State | Description |
|-------|-------------|
| `AVAILABLE` | Sufficient resources available for local execution |
| `CONSTRAINED` | Resources are limited but usable |
| `CRITICAL` | Resources are extremely limited, recommend cloud execution |

## Execution Targets

Based on resource state, the monitor recommends one of these execution targets:

| Target | Description |
|--------|-------------|
| `LOCAL_CPU` | Execute on local CPU |
| `LOCAL_GPU` | Execute on local GPU (if available and supported) |
| `CLOUD` | Execute on cloud model |

## Accessing Resource Metrics Directly

You can access the current resource metrics directly:

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
```