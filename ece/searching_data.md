# Searching Data

## Write and execute a search query for terms and/or phrases in one or more fields of an index

### Analyzed / Full Text Queries

#### Match Query

This query will match any words in the specified field.

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "match": {
            "example-field": "example 3"
        }
    }
}
```

#### Match Phrase Query

This query will match the exact phrase inside of a specific field.

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "match_phrase": {
            "example-field": "example 3"
        }
    }
}
```

#### Multi Match Query

This query returns results from search for a specific text on multiple fields.

Using '^' you can boost a specific field's relevancy score.

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "multi_match": {
            "query": "example-3",
            "fields": ['example-field^2","test-field"]
        }
    }
}
```

#### Query String

This query allows multiple phrases to be searched for in a specific field. It uses boolean operands 'AND' and 'OR' for the search.

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "query_string": {
            "default_field": "example-field",
            "query": "(example) AND (3)"
        }
    }
}
```

### Non-Analyzed Queries

#### Term Query

Looks for a character-sensitive exact match on a specific field.

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "term": {
            "example-field": {
                "value": "example-3"
            }
        }
    }
}
```
 
#### Terms Query

Looks for a character-sensitive exact match for multiple phrases on a specific field.

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "terms": {
            "example-field": ["example-3","test-3"]
        }
    }
}
```

#### Range Query

Looks for all documents that exist within a numerical or date range on a specific field.

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "range": {
            "example-ints": {
                "gte": "20",
                "lt": "80"
            }
        }
    }
}
```

#### Wildcard Search Query

Looks for all documents that match a phrase that uses '*' to match 0 or more characters

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "wildcard": {
            "example-field": {
                "value": "example*"
            }
        }
    }
}
```

#### Regex Query

Search for all documents that have a field with a value that matches the regex query

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "regexp": {
            "example-field": "example\-\d"
        }
    }
}
```

## Write and execute a search query that is a Boolean combination of multiple queries and filters

### Boolean Clauses

* must: All queries in this clause must return true.
* filter": Same as 'must' but the relevancy score will be ignored.
* must_not: All queries in this clause must return false.
* should: A specific amount of queries in this clause must be true.


Use the '_name' attribute to see which query matched the returned results.
Use the 'boost' attribute to increase a query's relevancy.

```elasticsearch_console_command
GET _search
{
    "query": {
        "bool":{
            "must": [
                {
                    "wildcard": {
                        "example-field": {
                            "_name": "Get Prefix",
                            "value": "exam*"
                        }
                    }
                }
            ],
            "filter": [
                "wildcard":{
                    "example-field": {
                        "value": "*ple*"
                    }
                }
            ],
            "must_not": [
                {
                    "match":{
                        "_name": "exclude example 3",
                        "example-field": "3"
                    }
                }
            ],
            "should":[
                {
                    "match":{
                        "boost": 3
                        "example-field": "1"
                    }
                },
                {
                    "match":{
                        "example-field": "2"
                    }
                }
            ],
            "minimum_should_match": 1
        }
    }
}
```

## Write an asynchronous search

Allows a search to be queued for execution.

| Endpoint                                  | Method | Description              |
| ----------------------------------------- | ------ | ------------------------ |
| /\<index-name\>/_async_search | POST | Submit an async search request |
| /_async_search/\<search_id\> | GET | Retrieve the results of a previously submitted async search |
| /_async_search/status/\<search_id\> | GET | Retrieve information about an async search's execution |
| /_async_search/\<search_id\> | DELETE | Remove an async's search results from  |

#### Create an Asynchronous search

```elasticsearch_console_command
POST /example-index/_async_search
{
    "query": {
        "match": {
            "example-field": "example"
        }
    }
}
```

#### Get the Status of an Asynchronous search

```elasticsearch_console_command
GET /_async_search/status/FmRldE8zREVEUzA2ZVpUeGs2ejJFUFEaMkZ5QTVrSTZSaVN3WlNFVmtlWHJsdzoxMDc=
```

#### Get the results from an Asynchronous Search

```elasticsearch_console_command
GET /_async_search/FmRldE8zREVEUzA2ZVpUeGs2ejJFUFEaMkZ5QTVrSTZSaVN3WlNFVmtlWHJsdzoxMDc=
```

#### Delete an Asynchronous Search request

```elasticsearch_console_command
DELETE /_async_search/FmRldE8zREVEUzA2ZVpUeGs2ejJFUFEaMkZ5QTVrSTZSaVN3WlNFVmtlWHJsdzoxMDc=
```

## Write and execute metric and bucket aggregations

### Request Body

```elasticsearch_console_command
{
   "query": {},
   "aggs": {
        "name-of-aggregations": {
            "aggregation-statement": {}
        }
   } 
}
```

### Return only Aggregations

By default Aggregations also return documents. To disable that use the size url parameter.

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "aggs": {
        "example-terms-aggregation-name": {}
    }
}
```

### Scope an aggregation

Aggregations process all documents returned in the search. Use the query parameter to filter the returned documents.

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "query": {
        "range": {
            "@timestamp": {
                "gte": "now-10d/d",
                "lt": "now/d"
            }
        }
    }
    "aggs": {
        "example-terms-aggregation-name": {}
    }
}
```

### Bucket Aggregations

Group documents into buckets, also called bins, based on field values, ranges, or other criteria.

#### Terms Aggregation

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "aggs": {
        "example-terms-aggregation-name": {
            "terms": {
                "field": "example-field.example-keyword",
                "size": 10
            }
        }
    }
}
```

#### Range Aggregation

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "aggs": {
        "example-range-aggregation-name": {
            "range": {
                "field": "example-number-field",
                "ranges": [
                    {
                        "to": 100
                    },
                    {
                        "from": 100,
                        "to": 500
                    },
                    {
                        "from": 500
                    }
                ] 
            }
        }
    }
}
```

#### Date Histogram Aggregation

##### Fixed Interval Example

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "aggs": {
        "example-date-histgram-aggregation-name": {
            "date_histogram": {
                "field": "@timestamp",
                "fixed_interval": "3m"
            }
        }
    }
}
```

##### Calendar Interval Example

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "aggs": {
        "example-date-histgram-aggregation-name": {
            "date_histogram": {
                "field": "@timestamp",
                "calendar_interval": "3m"
            }
        }
    }
}
```

### Metric Aggregations

Apply calculations to numerical data fields present in a document.

#### Cardinality Aggregation

Show distinct values of a field.

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "aggs": {
        "example-cardinality-aggregation-name": {
            "cardinality": {
                "field": "example-field"
            }
        }
    }
}
```

#### Sum Aggregation

Show distinct values of a field.

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "aggs": {
        "example-sum-aggregation-name": {
            "sum": {
                "field": "example-integer-field"
            }
        }
    }
}
```

## Write and execute aggregations that contain sub-aggregations

Aggregate the product of other aggregation functions

```elasticsearch_console_command
GET example-index/_search?size=0
{
    "example-parent-aggregation-name": {
        "terms": {
            "field": "example-field",
            "size": 10
        },
        "aggs": {
            "example-child-aggregation-name": {
                "avg": {
                    "field": "example-integer-field"
                }
            }
        }
    }
}
```

## Write and execute a query that searches across multiple clusters

```elasticsearch_console_command
GET example-cluster-name02:example-index,example-index/_search
{
    "query": {
        "match": {
            "example-field": "example-3"
        }
    }
}
```

## Write and execute a search that utilizes a runtime field
