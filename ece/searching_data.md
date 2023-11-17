# Searching Data

## Write and execute a search query for terms and/or phrases in one or more fields of an index

### Analyzed / Full Text Queries

#### Match Query

This query will match any words in the 

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

## Write and execute a search query that is a Boolean combination of multiple queries and filters

## Write an asynchronous search

## Write and execute metric and bucket aggregations

## Write and execute aggregations that contain sub-aggregations

## Write and execute a query that searches across multiple clusters

## Write and execute a search that utilizes a runtime field
