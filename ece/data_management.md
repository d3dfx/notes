# Data Management

## Define an index that satisfies a given set of requirements

### Create Index API Definition
| Endpoint | Method | Description | 
|----------|--------|-------------|
| /<index> |  PUT   | Create Index|

#### Request Body Schema

```
{
    "aliases": {
        "new_alias": {
           "filter": {
               "_comment": "This would be a Query DSL Object. Alias would only access the data matching the below query.:",
               "query": {
                   "match":{
                       "FIELD": "VALUE"
                   }
               }
           }, 
            "index_routing": "STRING",
            "is_hidden": "BOOL",
            "is_write_index": "BOOL ",
            "routing": "STRING",
            "search_routing": "STRING"
        },
    },
    "mappings":{
        "properties":{
            "FIELD": { 
                "type" : "STRING",
                "analyzer": "STRING",
                "coerce": "BOOL",
                "copy_to": "STRING",
                "doc_values": "BOOL",
                "dynamic": "BOOL",
                "eager_global_ordinals": "BOOL",
                "enabled": "BOOL",
                "format": "STRING",
                "ignore_above": "INTEGER",
                "ignore_malformed": "BOOL",
                "index": "BOOL",
                "index_options": "STRING",
                "index_phrases": "BOOL",
                "index_prefixes": {},
                "meta": {},
                "fields": {},
                "normalizer": "STRING",
                "norms": "BOOL",
                "null_value": "STRING",
                "position_increment_gap": "INTEGER",
                "properties": {},
                "search_analyzer": "STRING",
                "similarity": "STRING",
                "store": "BOOL",
                "subobjects": "BOOL",
                "term_vector": "STRING",
            }
        }
    },
    "settings:{
        "number_of_shards": "INTEGER",
        "number_of_routing_shards": "INTEGER",
    }
}
```
#### Alias Properties
| Property Name | Type | Description | Default |
|----------|--------|-------------|-----------|
| filter | QUERY DSL OBJECT | Define the query to issue when using the alias. | n/a |
| index_routing | STRING | Value used to route indexing operations to a specific shard. If specified, this overwrites the routing value for indexing operations. | n/a |
| is_hidden | BOOL | Makes alias only visible to Admins. All indicies with the alias must be hidden. | FALSE |
| is_write_index | BOOL | Makes this index the write index for the alias | FALSE |
| routing | STRING | Value used to route indexing and search operations to a specific shard. | n/a |
| search_routing | STRING | Value used to route search operations to a specific shard. If specified, this overwrites the routing value for search operations | n/a |

#### Mappings Properties
| Property Name | Type | Description | Default |
|----------|--------|-------------|----------|
| Type | DATA_TYPE | Explicitly assign the data type | n/a |
| analyzer | STRING | Assign an analyzer for the field | standard |
| coerce | BOOL | Numbers enclosed in quotes will be automatically converted to integers when indexed. i.e. "10" -> 10 | TRUE |
| copy_to | STRING | Copy & Concatenate field values into another field. The Field is not Indexed but is abled to be queried | n/a |
| doc_values | BOOL | Enable or disable a field being written to disk. If not written to disk it will be saved & query-able from the Inverse Index. | FALSE |
| dynamic | BOOL | Enable dynamic assignment on child properties when dynamic is disabled at the type level. | TRUE |
| eager_global_ordinals | BOOL | Allows keeping ordinals loaded after the initial call to load them. | TRUE |
| enabled | BOOL | Makes the field indexable and searchable. If False the field is stored but not searchable. | TRUE |
| format | STRING | Assign a date format Ex. dd-mm-yyyy | n/a |
| ignore_above | INTEGER | Do not index or store strings with a character count longer than the defined number | n/a |
| ignore_malformed | BOOL | Allows fields with incorrect data types to be ingested but they are not indexed | FALSE |
| index | BOOL | Controls whether or not fields are indexed | TRUE |
| index_options | STRING | Controls what information is added to the inverted index for search and highlighting purposes | positions |
| index_phrases | BOOL | If enabled, two-term word combinations (shingles) are indexed into a separate field. This allows exact phrase queries (no slop) to run more efficiently, at the expense of a larger index. | FALSE |
| index_prefixes | OBJECT | Enables the indexing of term prefixes to speed up prefix searches. | { "min_chars": 2, "max_chars": 20 } |
| meta | OBJECT | This metadata is opaque to Elasticsearch, it is only useful for multiple applications that work on the same indices to share meta information about fields. Ex. units of measurement | n/a |
| fields | OBJECT | Index the same field in different ways for different purposes. | n/a |
| normalizer | STRING | Property of keyword fields is similar to analyzer except that it guarantees that the analysis chain produces a single token. | n/a |
| norms | BOOL | Norms store various normalization factors that are later used at query time in order to compute the score of a document relatively to a query. Norms can take up a lot of disk space. Disable if not needed but they can not be reenabled. | TRUE |
| null_value | STRING | What string to replace explicitly null values with | n/a |
| position_increment_gap | INTEGER | When indexing text fields with multiple values a "fake" gap is added between the values to prevent most phrase queries from matching across the values. Modify the gap size with this param. | 100 |
| properties | OBJECT | Allows sub-fields to be defined | n/a |
| search_analyzer | STRING | Allows selection of an analyzer to use during search time | standard |
| similarity | STRING | Identify the scoring algorithm for a field | BM25 |
| store | STRING | Ensures that the original field value will be available without filtering the _source field. | FALSE |
| subobjects | BOOL | Allows dot pathed fields to be converted to objects. Disabling ensures these fields are not converted. | TRUE |
| term_vector | STRING | Term Vectors contain information about the terms produced by the analysis process. This parameter determines what term vector values are stored. | No |

* https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html
* https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html
* https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-params.html

## Define and use an index template for a given pattern that satisfies a given set of requirements

### Create Index Template API Definition
| Endpoint | Method | Description | 
|----------|--------|-------------|
| /_index_template/<index-template-name> |  PUT   | Create Index Template |

#### Request Body Schema

```
{
    "composed_of": [],
    "data_stream": {},
    "index_patterns": [],
    "_meta": {},
    "priority": "INTEGER",
    "template": {},
    "version": "INTEGER"
}
```
#### Create Index Template Properties
| Property Name | Type | Description | Default |
|----------|--------|-------------|----------|
| composed_of | ARRAY | An ordered list of component template names. | n/a |
| data_stream | OBJECT | If this object is included, the template is used to create data streams and their backing indices. Supports an empty object. | n/a |
| index_patterns | ARRAY | Array of wildcard (*) expressions used to match the names of data streams and indices during creation. | n/a |
| _meta | OBJECT | Optional user metadata about the index template. | n/a |
| priority | INTEGER | Priority to determine index template precedence when a new data stream or index is created. The index template with the highest priority is chosen. | 0 |
| template | OBJECT | Composed of parameters from the Create Index API. Reference Above! | n/a |
| version | INTEGER | Version number used to manage index templates externally. | n/a |


### Create Component Template API Definition
| Endpoint | Method | Description | 
|----------|--------|-------------|
| /_component_template/<component-template-name> | PUT | Create Component Template |

#### Request Body Schema

```

```

## Define and use a dynamic template that satisfies a given set of requirements

## Define an Index Lifecycle Management policy for a time-series index

## Define an index template that creates a new data stream

