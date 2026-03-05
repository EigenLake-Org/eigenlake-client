# EigenLake Python Client

`eigenlake` is the Python SDK for interacting with EigenLake API servers.

## Install

```bash
pip install eigenlake
```

## Quickstart

```python
import eigenlake
from eigenlake import schema as s

with eigenlake.connect(
    url="https://api.eigenlake.dev",
    api_key="<sk_sbx_your_api_key_here>",
) as client:
    schema, index_options = (
        s.SchemaBuilder(additional_properties=False)
        .add("document_id", s.string(required=True, filterable=True))
        .add("text", s.string(filterable=False))
        .build()
    )

    idx = client.indexes.create_or_get(
        namespace="demo-namespace",
        index="demo-index",
        dimensions=128,
        schema=schema,
        index_options=index_options,
    )

    idx.records.add(
        properties={"document_id": "doc-1", "text": "hello"},
        vector=[0.1] * 128,
    )
```

## Concepts

- `namespace`: top-level logical grouping for indexes.
- `index`: vector index name inside a namespace.
- `record`: one item with `properties` + `vector`.

## Next Steps

- Start with [Getting Started](getting-started.md)
- Learn schema design in [Schema Builder Guide](guides/schema-builder.md)
- Explore [Common Workflows](guides/common-workflows.md)
- Review [API Reference](api/client.md)
