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

## Write an asynchronous search

## Write and execute metric and bucket aggregations

## Write and execute aggregations that contain sub-aggregations

## Write and execute a query that searches across multiple clusters

## Write and execute a search that utilizes a runtime field
