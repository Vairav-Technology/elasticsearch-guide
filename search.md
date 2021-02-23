## Sample Search Query DSL

```json
GET /products/_search
{
  "query": {
    "match_all": {}
  }
}

```

```json
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1000,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
 }
}

```


## Sample Search Query from URL

The below search query is performed form URL. Here term is used to search for the name

```json
GET /products/_search?q=name:Lobster
```

```json
{
  "took" : 15,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 5,
      "relation" : "eq"
    },
    "max_score" : 5.833298,
    "hits" : [
      "..."
    ]
  }
}

```

Similarly, tags as well as terms can be used to searched from URL. Here boolean logic **AND** is used to join tags and terms.

```json
GET /products/_search?q=tags:Meat AND name:Tuna

```

```json
{
  "took" : 14,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 7.191996,
    "hits" : [
      "..."
    ]
  }
}

```

## Debugging search query with explain api

The search query can be debugged with explain api. This show why a document matched or did not matched the query.

```json
GET /products/_doc/1/_explain
{
  "query": {
    "term": {
      "name": "lobster"
    }
  }
}
```

```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "1",
  "matched" : false,
  "explanation" : {
    "value" : 0.0,
    "description" : "no matching term",
    "details" : [ ]
  }
}
```

## Simple term search query using query DSL

This search shows the simple usage of term query. This is usually used for exact match.

```json
GET /products/_search
{
  "query": {
    "term": {
      "is_active": true
    }
  }
}

```

```json
{
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 487,
      "relation" : "eq"
    },
    "max_score" : 0.72374225,
    "hits" : [
        "..."
    ]
  }
}

```


##  Multiple Term search query using query DSL

This search shows the simple usage of multiple term query. 

```json
GET /products/_search
{
  "query": {
    "terms": {
      "tags.keyword": [ "Soup", "Cake" ]
    }
  }
}

```

```json
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 44,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      "..."
    ]
  }
}

```


##  Term search query using IDs

This search shows the simple usage of term query for searching specific IDs. 

```json
GET /products/_search
{
  "query": {
    "ids": {
      "values": [ 1, 2, 3 ]
    }
  }
}

```

```json
{
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 3,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}

```


##  Range term search query

This search shows the simple usage of term query for searching range of data. 

```json
GET /products/_search
{
  "query": {
    "range": {
      "in_stock": {
        "gte": 1,
        "lte": 5
      }
    }
  }
}
```

```json

{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 96,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      "..."
    ]
  }
}

```

The range field can also be used for date and also be used with specific date format

```json
GET /products/_search
{
  "query": {
    "range": {
      "created": {
        "gte": "01-01-2010",
        "lte": "31-12-2010",
        "format": "dd-MM-yyyy"
      }
    }
  }
}
```

```json
{
  "took" : 4,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 57,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}

```

ES also supports relative date type. For example below subtracting 1 year from specific date.

```json
GET /products/_search
{
  "query": {
    "range": {
      "created": {
        "gte": "2010/01/01||-1y"
      }
    }
  }
}
```

```json
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 494,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}

```
Similar to example above, relative date can also be used for in following but not limited examples.

1. ```2010/01/01||-1y-1d``` : Subtracting 1 year and 1 day from specific date
2. ```2010/01/01||-1y/M``` : Subtracting 1 year from specific date and rounding by month
3. ```2010/01/01||/M-1y``` : Rounding by month before subtracting 1 year from specific date
4. ```now``` : relative to current date and timestamp
5. ```now/M-1y``` : Rounding by month before subtracting one year from **current date**



##  Term search query with non-null values

This search shows the simple usage of term query for searching non null values. 

```json
GET /products/_search
{
  "query": {
    "exists": {
      "field": "tags"
    }
  }
}
```

```json
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 316,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}
```


##  Term search query using prefixes

This search shows the simple usage of term query for searching using prefixes.

```json
GET /products/_search
{
  "query": {
    "prefix": {
      "tags.keyword": "Vege"
    }
  }
}

```

```json
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 34,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}
```

##  Term search query using wildcards and regular expression

This search shows the simple usage of term query for searching using wildcards like ```*``` for zero or more characters and ```?``` for any single character. The below example show the usage of ```*``` wildcard.

```json
GET /products/_search
{
  "query": {
    "wildcard": {
      "tags.keyword": "Veg*ble"
    }
  }
}

```
```json
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 34,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}
```
The below query shows the usage of ```?``` wildcard.

```json
GET /products/_search
{
  "query": {
    "wildcard": {
      "tags.keyword": "Veget?ble"
    }
  }
}
```
```json
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 34,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}
```
Not only limited to wildcards, the query can also be created using regular expression.

```json
GET /products/_search
{
  "query": {
    "regexp": {
      "tags.keyword": "Veg[a-zA-Z]+ble"
    }
  }
}
```
```json
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 34,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
        "..."
    ]
  }
}
```

## Simple full text query

This search shows the simple usage of full text query for searching.

```json
GET /recipe/_search
{
  "query": {
    "match": {
      "title": "Recipes with pasta or spaghetti"
    }
  }
}

```

```json
{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 17,
      "relation" : "eq"
    },
    "max_score" : 5.130475,
    "hits" : [
        "..."
    ]
  }
}

```

##  Match phrases with full text query

This search shows the simple usage of full text query for matching phrases. This is quite strict while searching like order of words matter as this searches a chunk of phrases on full text.

```json
GET /recipe/_search
{
  "query": {
    "match_phrase": {
      "title": "spaghetti puttanesca"
    }
  }
}

```

```json
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 3.5470161,
    "hits" : [
    "..."
    ]
  }
}

```

##  Multiple fields with one single full text query

This search shows the simple usage of full text query for matching multiple fields. 

```json
GET /recipe/_search
{
  "query": {
    "multi_match": {
      "query": "pasta",
      "fields": [ "title", "description" ]
    }
  }
}
```

```json
{
  "took" : 10,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 16,
      "relation" : "eq"
    },
    "max_score" : 1.0835493,
    "hits" : [
        "..."
    ]
  }
}

```


##  Querying with boolean logic

This search shows the use of boolean logic while searching for data matching multiple conditions.


```json
GET /recipe/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "ingredients.name": "parmesan"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "ingredients.name": "tuna"
          }
        }
      ],
      "should": [
        {
          "match": {
            "ingredients.name": "parsley"
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

```json

{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 2.1593752,
    "hits" : [
      "..."
    ]
  }
}

```
##  Debugging with boolean query with named query

This search shows the debug the boolean query while searching for data matching multiple conditions.This can be achieved by adding ```_name``` field to the query.

```json
GET /recipe/_search
{
    "query": {
        "bool": {
          "must": [
            {
              "match": {
                "ingredients.name": {
                  "query": "parmesan",
                  "_name": "parmesan_must"
                }
              }
            }
          ],
          "must_not": [
            {
              "match": {
                "ingredients.name": {
                  "query": "tuna",
                  "_name": "tuna_must_not"
                }
              }
            }
          ],
          "should": [
            {
              "match": {
                "ingredients.name": {
                  "query": "parsley",
                  "_name": "parsley_should"
                }
              }
            }
          ],
          "filter": [
            {
              "range": {
                "preparation_time_minutes": {
                  "lte": 15,
                  "_name": "prep_time_filter"
                }
              }
            }
          ]
        }
    }
}
```
This shows which result matched which bool query as pass that under ```matched_queries``` in hits.

```json
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 2.1593752,
    "hits" : [
      {
        "...",
        "matched_queries" : [
          "prep_time_filter",
          "parmesan_must",
          "parsley_should"
        ]
      }
      {
        "...",
        "matched_queries" : [
          "prep_time_filter",
          "parmesan_must"
        ]
      }
    ]
  }
}

```


# Nested Search 

THis type of query is used to query nested fields in the index.

```json
GET /department/_search
{
  "query": {
    "nested": {
      "path": "employees",
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "employees.position": "intern"
              }
            },
            {
              "term": {
                "employees.gender.keyword": {
                  "value": "F"
                }
              }
            }
          ]
        }
      }
    }
  }
}
```

```json
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 2.3905568,
    "hits" : [
      "..."
    ]
  }
}

```

# Nested Search with inner hits

This type of query is used to query nested fields in the index with inner hits. Inner hits enhances the nested search to select only specific part of a document.

```json
GET /department/_search
{
  "_source": false,
  "query": {
    "nested": {
      "path": "employees",
      "inner_hits": {},
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "employees.position": "intern"
              }
            },
            {
              "term": {
                "employees.gender.keyword": {
                  "value": "F"
                }
              }
            }
          ]
        }
      }
    }
  }
}
```

```json
{
  "took" : 7,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 2.3905568,
    "hits" : [
      {
        "_index" : "department",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 2.3905568,
        "inner_hits" : {
          "employees" : {
            "hits" : {
              "total" : {
                "value" : 1,
                "relation" : "eq"
              },
              "max_score" : 2.3905568,
              "hits" : [
                {
                  "_index" : "department",
                  "_type" : "_doc",
                  "_id" : "1",
                  "_nested" : {
                    "field" : "employees",
                    "offset" : 3
                  },
                  "_score" : 2.3905568,
                  "_source" : {
                    "gender" : "F",
                    "name" : "Julie Powell",
                    "position" : "Intern",
                    "age" : 26
                  }
                }
              ]
            }
          }
        }
      },
      "..."
    ]
  }
}

```
