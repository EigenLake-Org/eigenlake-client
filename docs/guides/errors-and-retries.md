# Errors and Retries

The SDK raises typed exceptions from `eigenlake.errors`.

## Error Types

- `AuthenticationError`: invalid or missing API key.
- `NotFoundError`: namespace/index/object does not exist.
- `ConflictError`: duplicate conflicts.
- `ValidationError`: invalid request payload.
- `APIError`: non-typed API errors (including 5xx after retries).
- `NetworkError`: request/network failures.

## Retry Behavior

`eigenlake.connect(..., retries=2)` controls retry count.

- Network request errors are retried up to `retries`.
- Server `5xx` responses are retried up to `retries`.
- Client errors (`4xx`) are not retried.

## Example

```python
import eigenlake
from eigenlake.errors import AuthenticationError, ValidationError, APIError, NetworkError

client = eigenlake.connect(
    url="https://api.eigenlake.dev",
    api_key="<sk_sbx_your_api_key_here>",
    timeout=20.0,
    retries=2,
)

try:
    idx = client.indexes.open(namespace="demo-namespace", index="demo-index")
    idx.records.add(
        properties={"document_id": "doc-1"},
        vector=[0.1] * 128,
    )
except AuthenticationError as exc:
    print("auth error:", exc)
except ValidationError as exc:
    print("validation error:", exc)
except NetworkError as exc:
    print("network error:", exc)
except APIError as exc:
    print("api error:", exc)
finally:
    client.close()
```
