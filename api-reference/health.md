# Health Check API

The Health Check endpoint allows you to verify that the Oblix server is running correctly and check its version.

## Endpoint: `/health`

**Method:** GET

**Description:** Check the health status of the Oblix server

## Response Structure

```json
{
  "status": "healthy",
  "version": "0.1.2"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `status` | string | Current server status ("healthy" when server is running correctly) |
| `version` | string | Current version of the Oblix server |

## Example Request

```bash
curl http://localhost:62549/health
```

## Example Response

```json
{
  "status": "healthy",
  "version": "0.1.2"
}
```

## Status Codes

| Status Code | Description |
|-------------|-------------|
| 200 | Server is healthy and running |
| 500 | Server is experiencing issues |

## Python Example

```python
import requests

# Check if the server is healthy
response = requests.get("http://localhost:62549/health")
data = response.json()

if response.status_code == 200 and data.get("status") == "healthy":
    print(f"Server is healthy! Version: {data.get('version')}")
else:
    print("Server might be experiencing issues")
```

## JavaScript Example

```javascript
fetch("http://localhost:62549/health")
  .then(response => response.json())
  .then(data => {
    if (data.status === "healthy") {
      console.log(`Server is healthy! Version: ${data.version}`);
    } else {
      console.log("Server might be experiencing issues");
    }
  })
  .catch(error => {
    console.error("Error checking server health:", error);
  });
```

## Usage Recommendations

- Use this endpoint for monitoring the health of your Oblix server in production environments
- Integrate with your monitoring systems to alert on server issues
- Check the server version to ensure compatibility with your client code