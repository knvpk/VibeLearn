# Obsidian Bases — `.base` file specs

One file per collection, written to `{wiki.root}bases/<collection>.base`.
All files are written once on first node write of that type; never overwritten.

---

## `concepts.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "concept" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Concepts — All",
      "columns": ["name", "difficulty", "tags"]
    },
    {
      "id": "board",
      "type": "board",
      "name": "Concepts — By Difficulty",
      "groupBy": "difficulty"
    }
  ]
}
```

## `sources.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "source" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Sources — All",
      "columns": ["title", "type", "author", "date_ingested"]
    },
    {
      "id": "board",
      "type": "board",
      "name": "Sources — By Type",
      "groupBy": "type"
    }
  ]
}
```

## `tools.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "tool" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Tools — All",
      "columns": ["name", "category", "maturity", "docker"]
    },
    {
      "id": "board",
      "type": "board",
      "name": "Tools — By Category",
      "groupBy": "category"
    }
  ]
}
```

## `workflows.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "workflow" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Workflows — All",
      "columns": ["name", "status", "difficulty"]
    },
    {
      "id": "board",
      "type": "board",
      "name": "Workflows — By Status",
      "groupBy": "status"
    }
  ]
}
```

## `ideas.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "idea" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Ideas — All",
      "columns": ["title", "status", "priority"]
    },
    {
      "id": "board",
      "type": "board",
      "name": "Ideas — By Status",
      "groupBy": "status"
    }
  ]
}
```

## `os.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "os" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "OSes — All",
      "columns": ["name", "family", "form_factor"]
    },
    {
      "id": "board",
      "type": "board",
      "name": "OSes — By Family",
      "groupBy": "family"
    }
  ]
}
```

## `authors.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "author" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Authors — All",
      "columns": ["name", "company", "verdict"]
    }
  ]
}
```

## `terms.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "term" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Terms — All",
      "columns": ["name", "tags"]
    }
  ]
}
```

## `collections.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "collection" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Collections — All",
      "columns": ["title", "catalog_type", "date_ingested"]
    }
  ]
}
```

## `languages.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "language" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Languages — All",
      "columns": ["name", "execution"]
    }
  ]
}
```

## `companies.base`

```json
{
  "filters": {
    "conditions": [
      { "field": "collection", "operator": "equals", "value": "company" }
    ]
  },
  "views": [
    {
      "id": "table",
      "type": "table",
      "name": "Companies — All",
      "columns": ["name", "founded", "verdict"]
    }
  ]
}
```
