# eigenlake

Python SDK for EigenLake.

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
        .add("created_at", s.datetime(filterable=True))
        .build()
    )

    idx = client.indexes.create_or_get(
        namespace="demo-namespace",
        index="demo-index",
        dimensions=128,
        schema=schema,
        index_options=index_options,
    )

    record_id = idx.records.add(
        properties={"document_id": "doc-1", "text": "hello"},
        vector=[0.1] * 128,
    )

    result = idx.search.nearest(
        vector=[0.1] * 128,
        limit=3,
    )
    print(record_id, result)
```

## Local Development

```python
import eigenlake

client = eigenlake.connect_local(
    host="http://localhost",
    port=8000,
    api_key="<your_api_key>",
)
```

## Docs

- Documentation source: `docs/`
- Docs site config: `mkdocs.yml`

```bash
pip install -e ".[docs]"
mkdocs serve
```

Docs will be available at `http://127.0.0.1:8000`.
