## Formatting the result

For returning the data in YAML format

```json
GET /recipe/_search?format=yaml
{
  "query": {
    "match": {
      "title": "pasta"
    }
  }
}
```

For returning prettified json.

```json
GET /recipe/_search?pretty
{
  "query": {
    "match": {
      "title": "pasta"
    }
  }
}
```


## Source Filtering

For disabling the source filter as a whole.
```json
GET /recipe/_search
{
  "_source": false,
  "query": {
    "match": { "title": "pasta" }
  }
}
```

For filtering the data by source

```json
GET /recipe/_search
{
  "_source": [ "ingredients.*", "servings" ],
  "query": {
    "match": { "title": "pasta" }
  }
}
```

Including all of the ingredients object's keys, except the name key

```json
GET /recipe/_search
{
  "_source": {
    "includes": "ingredients.*",
    "excludes": "ingredients.name"
  },
  "query": {
    "match": { "title": "pasta" }
  }
}
```

## Specifying the result size
Limiting the size of the result can be done by using ```size``` field.

```json
GET /recipe/_search
{
  "_source": false,
  "size": 2,
  "query": {
    "match": {
      "title": "pasta"
    }
  }
}
```

## Specifying the result offset
When the data has to be started at some offset, ```from``` field can be used to offset the data is returned.
```json
GET /recipe/_search
{
  "_source": false,
  "size": 2,
  "from": 2,
  "query": {
    "match": {
      "title": "pasta"
    }
  }
}
```


## Specifying the result sorting
When data has to be sorted, ```sort``` field can be used to sort. Also multiple fields can be used to sort the data.

```json
GET /recipe/_search
{
  "_source": [ "preparation_time_minutes", "created" ],
  "query": {
    "match_all": {}
  },
  "sort": [
    { "preparation_time_minutes": "asc" },
    { "created": "desc" }
  ]
}
```

## Specifying the result sorting by multi value fields
When data has to be sorted, ```sort``` field can be used to sort. Here multi-value fields are used.

```json
GET /recipe/_search
{
  "_source": "ratings",
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "ratings": {
        "order": "desc",
        "mode": "avg"
      }
    }
  ]
}

```

## Adding filter clause to bool query

```json
GET /recipe/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "pasta"
          }
        }
      ],
      "filter": [
        {
          "range": {
            "preparation_time_minutes": {
              "lte": 15
            }
          }
        }
      ]
    }
  }
}
```
