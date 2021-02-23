
## Creating new index
New Index can be created using **PUT** HTTP verb. Here adding new index named pages.

```
PUT /pages
```

```json
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "pages"
}


```

## Creating new index with custom no of shards and replica shards
New Index can be created using **PUT** HTTP verb. Here adding new index named pages.

```json
PUT /products
{
"settings": {
  "number_of_shards": 2,
  "number_of_replicas": 2
}
}
```

```json
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "products"
}
```

## Deleting index
New Index can be deleted using **DELETE** HTTP verb. Here deleting  index named pages.

```
DELETE /pages
```

```json
{
  "acknowledged" : true
}


```
## Indexing a document (Inserting)
A document can be "index" or add document to index by using **POST** HTTP verb. Here indexing a new document to products index

```json
POST /products/_doc
{
  "name": "Coffee Maker",
  "price": 64,
  "in_stock": 10
}
```

```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "G6eO0XYB4fSekbkUnMuv",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

For a custom id or "_id" field we use **PUT** 

```json
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 49,
  "in_stock": 4
}
```

```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

## Retrieving a specific document 
A document can be retrieve from index by using **GET** HTTP verb.

```json
GET /products/_doc/100
```
```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "Toaster",
    "price" : 49,
    "in_stock" : 4
  }
}
```


## Updating a document 
A document can be updated by using **POST** HTTP verb. Although ES documents are immutable, ES will add the respective change and *replace* the document with updated details.
Here in_stock value is updated of document id ```100```

```json
POST /products/_update/100
{
  "doc": {
    "in_stock": 3
  }
}
```

```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}
```
New field also can be added to the existing document by using ```tags``` field.
```json
POST /products/_update/100
{
  "doc": {
    "tags": ["electronics"]
  }
}
```
```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 3,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 2,
  "_primary_term" : 1
}
```

## Updating a specific document using script (Scripted Updates)

ES supports scripting which allows to write a custom logic and custom code while accessing a documents value. Results field after update query will send ```updated``` if success and ```noop``` if updated with same value .First request show how to decrease the value by 1.

```json
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}
```

```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 4,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}


```
Request show how to set the value to 10.

```json
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock = 10"
  }
}
```

```json
 {
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 4,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}


```

Request show how to set the value passed according to params.

```json
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= params.quantity",
    "params": {
        "quantity": 4
    }
  }
}
```
```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 4,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}


```

Request show how to set the value passed according to params.
This request basically decreases the stock and if it falls below 0 deletes the whole document.
```json
POST /products/_update/100
{
  "script": {
    "source": """
        if(ctx._source.in_stock >= 1){
            ctx._source.in_stock--;
        }else{
            # or delete it 
            ctx.op='delete'
        }
    """
  }
}
```
```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 4,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}

```

## Updating a specific document using script (Upserts)
Conditionally adding or updating the document can be done using upserts. Below query will first checks to add to the stock. if not just add new document.

```json
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock++"
  },
  "upsert": {
    "name": "Blender",
    "price": 399,
    "in_stock": 5
  }
}
```
```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "101",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 4,
  "_primary_term" : 1
}
```
Running the above query second time updates it.
```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "101",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 5,
  "_primary_term" : 1
}
```


## Updating multiple document by query
ES also supports update by query which updates multiple document with matching query by using update by query API.


```json
POST /products/_update_by_query
{
  "script": {
    "source": "ctx._source.in_stock--"
  },
  "query": {
    "match_all": {}
  }
}
```
```json
{
  "took" : 17,
  "timed_out" : false,
  "total" : 2,
  "updated" : 2,
  "deleted" : 0,
  "batches" : 1,
  "version_conflicts" : 0,
  "noops" : 0,
  "retries" : {
    "bulk" : 0,
    "search" : 0
  },
  "throttled_millis" : 0,
  "requests_per_second" : -1.0,
  "throttled_until_millis" : 0,
  "failures" : [ ]
}
```


## Replacing a specific document 
Replacing a whole document by given id can be performed using **PUT** HTTP verb.


```json
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 49,
  "in_stock": 4
}
```
```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "100",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 5,
  "_primary_term" : 1
}
```
## Deleting a specific document 
Replacing a whole document by given id can be performed using **DELETE** HTTP verb.


```json
DELETE /products/_doc/101
```

```json
{
  "_index" : "products",
  "_type" : "_doc",
  "_id" : "101",
  "_version" : 3,
  "result" : "deleted",
  "_shards" : {
    "total" : 3,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 6,
  "_primary_term" : 1
}

```

## Deleting multiple document by query
ES also supports deleting by query which deletes multiple document with matching query by using update by query API.


```json
POST /products/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```
```json
{
  "took" : 17,
  "timed_out" : false,
  "total" : 2,
  "updated" : 2,
  "deleted" : 0,
  "batches" : 1,
  "version_conflicts" : 0,
  "noops" : 0,
  "retries" : {
    "bulk" : 0,
    "search" : 0
  },
  "throttled_millis" : 0,
  "requests_per_second" : -1.0,
  "throttled_until_millis" : 0,
  "failures" : [ ]
}
```

## CRUD with Bulk API
For doing CRUD task in bulk, ES provides bulk api. Here first line shows the metadata of the index and document we want to perform CRUD on and second line shows the exact data we want to create,update or delete.

```json
POST /_bulk
{ "index": { "_index": "products", "_id": 200 } }
{ "name": "Espresso Machine", "price": 199, "in_stock": 5 }
{ "create": { "_index": "products", "_id": 201 } }
{ "name": "Milk Frother", "price": 149, "in_stock": 14 }
```

```json
{
  "took" : 4,
  "errors" : false,
  "items" : [
    {
      "index" : {
        "_index" : "products",
        "_type" : "_doc",
        "_id" : "200",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 3,
          "successful" : 2,
          "failed" : 0
        },
        "_seq_no" : 2,
        "_primary_term" : 1,
        "status" : 201
      }
    },
    {
      "create" : {
        "_index" : "products",
        "_type" : "_doc",
        "_id" : "201",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 3,
          "successful" : 2,
          "failed" : 0
        },
        "_seq_no" : 3,
        "_primary_term" : 1,
        "status" : 201
      }
    }
  ]
}

```

Similarly bulk api can be used to update or even delete the documents


```json
POST /_bulk
{ "update": { "_index": "products", "_id": 201 } }
{ "doc": { "price": 129 } }
{ "delete": { "_index": "products", "_id": 200 } }
```
```json
{
  "took" : 10,
  "errors" : false,
  "items" : [
    {
      "update" : {
        "_index" : "products",
        "_type" : "_doc",
        "_id" : "201",
        "_version" : 2,
        "result" : "updated",
        "_shards" : {
          "total" : 3,
          "successful" : 2,
          "failed" : 0
        },
        "_seq_no" : 4,
        "_primary_term" : 1,
        "status" : 200
      }
    },
    {
      "delete" : {
        "_index" : "products",
        "_type" : "_doc",
        "_id" : "200",
        "_version" : 2,
        "result" : "deleted",
        "_shards" : {
          "total" : 3,
          "successful" : 2,
          "failed" : 0
        },
        "_seq_no" : 5,
        "_primary_term" : 1,
        "status" : 200
      }
    }
  ]
}
```

