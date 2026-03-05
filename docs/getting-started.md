# Getting Started

## 1. Connect to EigenLake

```python
import eigenlake

client = eigenlake.connect(
    url="https://api.eigenlake.dev/",
    api_key="<sk_sbx_your_api_key_here>",
)
```

You can also connect to local API deployments:

```python
client = eigenlake.connect_local(
    host="http://localhost",
    port=8000,
    api_key="<your_api_key>",
)
```

## 2. Define schema and index options

```python
from eigenlake import schema as s
```

```python
schema, index_options = (
    s.SchemaBuilder(additional_properties=False)
    .add("document_id", s.string(required=True, filterable=True))
    .add("document_title", s.string(filterable=True))
    .add("chunk_number", s.integer(filterable=True))
    .add("document_url", s.string(format="uri", filterable=True))
    .add("created_at", s.datetime(filterable=True))
    .add("tags", s.array(s.string(), filterable=False, max_items=20))
    .build()
)
```

## 3. Create or open an index

```python
idx = client.indexes.create_or_get(
    namespace="demo-namespace",
    index="demo-index",
    dimensions=128,
    schema=schema,
    index_options=index_options,
)
```

Or open an existing index:

```python
idx = client.indexes.open(
    namespace="demo-namespace",
    index="demo-index",
)
```

## 4. Insert records

```python
record_id = idx.records.add(
    properties={"document_id": "doc-1", "text": "hello"},
    vector=[0.1] * 128,
)
print("inserted:", record_id)
```

Bulk insert:

```python
result = idx.records.add_many(
    [
        {"id": "doc-2", "properties": {"document_id": "doc-2", "text": "hello 2"}, "vector": [0.2] * 128},
        {"id": "doc-3", "properties": {"document_id": "doc-3", "text": "hello 3"}, "vector": [0.3] * 128},
    ],
    on_error="continue",
)

print("inserted:", len(result))
print("errors:", result.number_errors)
```

## 5. Search and read

```python
result = idx.search.nearest(
    vector=[0.1] * 128,
    limit=5,
)
print(result)
```

Fetch by id:

```python
obj = idx.records.get("doc-1")
print(obj)
```

## 6. Close client

```python
client.close()
```

Use context manager to close automatically:

```python
with eigenlake.connect(url="https://api.eigenlake.dev/", api_key="<sk_sbx_your_api_key_here>") as client:
    print(client.ready())
```
