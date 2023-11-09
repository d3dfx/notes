# Data Management

## Define an index that satisfies a given set of requirements

### API Index Definition
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
                "
            }
        }
    }
}
```
#### Alias Properties
| Property Name | Type | Description | 
|----------|--------|-------------|
| filter | QUERY DSL OBJECT | Define the query to issue when using the alias. |
| index_routing | STRING | Value used to route indexing operations to a specific shard. If specified, this overwrites the routing value for indexing operations. |
| is_hidden | BOOL | Makes alias only visible to Admins. All indicies with the alias must be hidden. |
| is_write_index | BOOL | Makes this index the write index for the alias |
| routing | STRING | Value used to route indexing and search operations to a specific shard. |
| search_routing | STRING | Value used to route search operations to a specific shard. If specified, this overwrites the routing value for search operations |

#### Mappings Properties
| Property Name | Type | Description | 
|----------|--------|-------------|
| Type | DATA_TYPE | Explicitly assign the data type |
| analyzer | STRING | Assign an analyzer for the field |
| coerce | BOOL | Numbers enclosed in quotes will be automatically converted to integer numbers. i.e. "10" -> 10 |
| copy_to | STRING | Copy & Concatenate field values into another field. The Field is not Indexed but is abled to be queried |
| doc_values | BOOL | Enable or disable a field being written to disk. If not written to disk it will be saved & query-able from the Inverse Index. |


### Links
* https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html
* https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html
* https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-params.html

## Define and use an index template for a given pattern that satisfies a given set of requirements

## Define and use a dynamic template that satisfies a given set of requirements

## Define an Index Lifecycle Management policy for a time-series index

## Define an index template that creates a new data stream

