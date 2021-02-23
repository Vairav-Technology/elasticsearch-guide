# Definitions
> Use Ctrl + F to search and jump to  specific words


## Node

Node is instance of ES and not actually refers to the machine itself. So we can run 5 nodes (instances of ES) in single machine.**This is not recommended on production though.**


## Cluster

Cluster is collection of related noted that contain all of out data. We can have multiple clusters if we want but 1 is usually enough.
when a new node starts up, it will either join an existing cluster (if configured) or will create cluster with that single node.


## Document

Each unit of data that we store within out cluster is called document. Document are JSON object containing the data. When **indexing** the document, ES will store the original JSON object as well as metadata of ES.

## Index

Index is a collection of documents that have similar characteristics. They groups documents together logically as well as provide configuration options that are related to scalability and availability.
I think of it as Table in SQL.


## Sharding

Sharding is a way to divide an index into separate pieces where each piece is called shards. This is to be noted that sharding occurs on index level. Since ES is built on top of Apache Lucene, each shard is actually Apache Lucene index.

## Replication

Replication is a way to copy an index and store it into different node for fault tolerance. These copies are called replica shards. Shards that are replicated are called primary shard. Replica Shards are never stored on the same node as primary shard. This means if one node fails, one copy of data is safe.

## Node Roles
Some of the nodes and it's roles are described briefly,

1. Master
>A node that has the master role (default), which makes it eligible to be elected as the master node, which controls the cluster.

2. Data
>A node that has the data role (default). Data nodes hold data and perform data related operations such as CRUD, search, and aggregations. A node with the data role can fill any of the specialized data node roles.

3. Ingest
>A node that has the ingest role (default). Ingest nodes are able to apply an ingest pipeline to a document in order to transform and enrich the document before indexing. With a heavy ingest load, it makes sense to use dedicated ingest nodes and to not include the ingest role from nodes that have the master or data roles.

4. Machine Learning
>A node that has xpack.ml.enabled and the ml role, which is the default behavior in the Elasticsearch default distribution. If you want to use machine learning features, there must be at least one machine learning node in your cluster. For more information about machine learning features, see Machine learning in the Elastic Stack.

5. Voting Only
>A voting-only master-eligible node is a node that participates in master elections but which will not act as the cluster’s elected master node. In particular, a voting-only node can serve as a tiebreaker in elections.

## Routing
Routing is the process of resolving a shard for a document weather it be while retrieving or storing a document.The default routing formula is shown below

> ```  shard_num = hash( _routing ) % num_primary_shard ```

## Primary Terms
Primary terms are a way for ES to distinguish between old and new primary shards when the primary shard of replication group has changed. It is essentially just a counter for how many times a primary shard has changed. That counter is known as sequence number.

## Mappings
Mappings defines how the documents and their fields that are to be stored and indexed. This is done so that the fields are of consistent and accurate data type i.e numbers, text, DateTime or even geographical location. Similar to a schema which is present on SQL databases. ES supports explicit mapping in which we define mapping and dynamic mapping which ES guesses and dynamically allocate the datatype. A field may have multiple mappings.

## Meta Fields
Every document that is stored within an ES cluster, has some meta-data associated with them, apart from the data fields that we specify when indexing documents.These fields are called meta fields. Some of the meta fields used in ES are :

1. _index
>This field is added to documents automatically and is used by ES internally.It contains the name of the index to
which the document belongs. This is used internally when querying documents within an index, but may also be used explicitly within search queries if searching for documents within multiple indices, for instance.

2. _id
>The “_id” meta field stores the ID of documents and can be queried within certain queries.

3. _source
>The “_source” meta field contains the original JSON object that was passed to Elasticsearch when indexing the document. This field is not indexed, and therefore cannot be searched, but can be retrieved.

4. _field_names
>The “_field_names” meta field contains the names of every field that contains a non-null value. This is used with a query named “exists,”. For instance, which documents that contain a non-null value for a given field.

5. _routing
>ES routes documents to shards. If custom routing is used to route documents to shards based on a specified value, then this value is stored within the “_routing”.

6. _version
> ES uses versioning of documents internally with a meta field named “_version.”. If you retrieve a document by ID, this meta
field will be part of the result.The value is simply an integer which starts at one and is incremented every time we change a document.

## Leaf queries and compound queries
1. Leaf Queries : Leaf queries look for specific values in certain field/fields. These queries can be used independently. Some of these queries include match, term, range queries.

2. Compound queries : Compound queries uses the combination of leaf/compound queries. Basically, they combine multiple queries together to achieve their target results.

## Relevance Score
ES, by default, while returning the search results, would sort them based on their relevance score, which indicates how well the document matches the query. This relevance score is computed and returned in the _score parameter of the metadata with each result.

## Query context
In the query context, a query clause answers the question “How well does this document match this query clause?” Besides deciding whether or not the document matches, the query clause also calculates a relevance score in the _score metadata field. Query context is in effect whenever a query clause is passed to a query parameter, such as the query parameter in the search API.

## Filter context
In a filter context, a query clause answers the question “Does this document match this query clause?” The answer is a simple Yes or No — no scores are calculated. Filter context is mostly used for filtering structured data, e.g. Does this timestamp fall into the range 2015 to 2016? Is the status field set to "published"? Frequently used filters will be cached automatically by Elasticsearch, to speed up performance.Filter context is in effect whenever a query clause is passed to a filter parameter, such as the filter or must_not parameters in the bool query, the filter parameter in the constant_score query, or the filter aggregation.



## Term vs Full Text queries

|             | Term-level queries | Full-text queries |
| ----------- | :----------- | :----------- |
| Description | Term-level queries answer which documents match a query.|Full-text queries answer how well the documents match a query.|
| Analyzer    | The search term isn’t analyzed. This means that the term query searches for your search term as it is.|The search term is analyzed by the same analyzer that was used for the specific field of the document at the time it was indexed. This means that your search term goes through the same analysis process that the document’s field did.|
| Relevance   |Term-level queries simply return documents that match without sorting them based on the relevance score. They still calculate the relevance score, but this score is the same for all the documents that are returned.|Full-text queries calculate a relevance score for each match and sort the results by decreasing order of relevance.|
| Use Case    |Use term-level queries when you want to match exact values such as numbers, dates, tags, and so on, and don’t need the matches to be sorted by relevance.|Use full-text queries to match text fields and sort by relevance after taking into account factors like casing and stemming variants.|

> Source: https://opendistro.github.io/for-elasticsearch-docs/docs/elasticsearch/term/


## Bool Query
A query that matches documents matching boolean combinations of other queries. The bool query maps to Lucene BooleanQuery. It is built using one or more boolean clauses, each clause with a typed occurrence. The occurrence types are:


1. must

The clause (query) must appear in matching documents and will contribute to the relevance score.

2. filter

The clause (query) must appear in matching documents. However unlike must the score of the query will be ignored. Filter clauses are executed in filter context, meaning that (relevance) scoring is ignored and clauses are considered for caching.

3. should

The clause (query) should appear in the matching document. In a boolean query with no must or filter clauses, one or more should clauses must match a document. The minimum number of should clauses to match can be set using the minimum_should_match parameter.

4. must_not

The clause (query) must not appear in the matching documents.


## Joining queries
Performing full SQL-style joins in a distributed system like Elasticsearch is prohibitively expensive. Instead, Elasticsearch offers two forms of join which are designed to scale horizontally.

1. nested query
Documents may contain fields of type nested. These fields are used to index arrays of objects, where each object can be queried (with the nested query) as an independent document.
2. has_child and has_parent queries
A parent-child relationship can exist between two document types within a single index. The has_child query returns parent documents whose child documents match the specified query, while the has_parent query returns child documents whose parent document matches the specified query.





