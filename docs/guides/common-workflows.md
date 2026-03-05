# Common Workflows

## Open Index Handle

```python
idx = client.indexes.open(
    namespace="demo-namespace",
    index="demo-index",
)
```

## Insert One Record

```python
record_id = idx.records.add(
    id="doc-1",
    properties={"document_id": "doc-1", "text": "hello"},
    vector=[0.1] * 128,
)
```

## Insert Many Records

```python
result = idx.records.add_many(
    [
        {"id": "doc-2", "properties": {"document_id": "doc-2"}, "vector": [0.2] * 128},
        {"id": "doc-3", "properties": {"document_id": "doc-3"}, "vector": [0.3] * 128},
    ],
    on_error="continue",
)

print("inserted:", len(result))
print("error_count:", result.number_errors)
print("failed_records:", result.failed_records)
```

## High-Throughput Batch Helper

```python
with idx.batch.with_size(batch_size=200, max_workers=4, on_error="continue") as batch:
    batch.add(id="doc-10", properties={"document_id": "doc-10"}, vector=[0.1] * 128)
    batch.add(id="doc-11", properties={"document_id": "doc-11"}, vector=[0.2] * 128)

print("error_count:", batch.number_errors)
```

## Nearest Search

```python
search_result = idx.search.nearest(
    vector=[0.1] * 128,
    limit=10,
    filter={"document_id": {"$eq": "doc-1"}},
)
```

## List and Iterate

```python
page = idx.search.list(limit=100, offset=0)
print(page)

for obj in idx.search.iterate(page_size=500):
    print(obj)
```

## Update and Replace

```python
idx.records.update(
    id="doc-1",
    properties={"text": "updated"},
)

idx.records.replace(
    id="doc-1",
    properties={"document_id": "doc-1", "text": "replaced"},
    vector=[0.4] * 128,
)
```

## Delete

```python
idx.records.remove("doc-1")

job = idx.records.remove_many(
    filter={"document_id": {"$in": ["doc-2", "doc-3"]}},
    background=True,
)
print(job)
```

## Index Metadata and Maintenance

```python
print(idx.settings.dimensions())
print(idx.settings.schema())
print(idx.settings.shards())

idx.manage.remove_by_filter(
    filter={"created_at": {"$lt": "2024-01-01T00:00:00Z"}},
    background=True,
)
```
