# Schema Builder Guide

Use `eigenlake.schema.SchemaBuilder` to produce:

- JSON schema for metadata validation
- index options used by EigenLake metadata filtering

## Basic Example

```python
from eigenlake import schema as s

schema, index_options = (
    s.SchemaBuilder(additional_properties=False)
    .add("document_id", s.string(required=True, filterable=True))
    .add("document_title", s.string(filterable=True))
    .add("chunk_number", s.integer(filterable=True))
    .add("created_at", s.datetime(filterable=True))
    .add("score", s.number(filterable=True, minimum=0.0, maximum=1.0))
    .add("is_public", s.boolean(filterable=True))
    .add("tags", s.array(s.string(), filterable=False, max_items=20))
    .build()
)
```

## Field Helpers

- `s.string(...)`
- `s.integer(...)`
- `s.number(...)`
- `s.boolean(...)`
- `s.array(item, ...)`
- `s.datetime(...)`
- `s.date(...)`

## Required vs Filterable

- `required=True`: field appears in schema `required` list.
- `filterable=True`: field can be used in server-side filters.
- `filterable=False`: included in metadata but excluded from filter indexes.

## Common String Options

```python
s.string(
    required=True,
    filterable=True,
    format="uri",
    min_length=1,
    max_length=500,
    pattern=r"^https?://",
)
```

## Array Fields

```python
s.array(
    s.string(),
    min_items=1,
    max_items=10,
    unique_items=True,
    filterable=False,
)
```

## Use with `create_or_get`

```python
idx = client.indexes.create_or_get(
    namespace="demo-namespace",
    index="demo-index",
    dimensions=128,
    schema=schema,
    index_options=index_options,
)
```
