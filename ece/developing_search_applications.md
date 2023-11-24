# Developing Search Applications

## Highlight the search terms in the response of a query

Default tags used are `<em></em>`

```elasticsearch_console_command
GET example-index/_search
{
    "query": {
        "match": {
            "example-field": "example"
        }
    },
    "highlight":{
        "pre_tags": [
            "<tag>"
        ],
        "post_tags":[
            "</tag>"
        ],
        "fields":{
            "example-field": {}
        }
    }
}
```

## Sort the results of a query by a given set of requirements

```elasticsearch_console_command
GET example-index/_search
{
    "sort": [
        {
            "example-field": {
                "order": "asc"
            }
        }
    ]
}
```

## Implement pagination of the results of a search query

### Parameters

| Name | Description     |
| --------------- | --------------- |
| from | Define the number of results to skip |
| size | Maximum number of hits to return |

Hard size limit set by `index.max_result_window` index setting. Default max is 10,000.

### Query

```elasticsearch_console_command
GET example-index/_search
{
    "from": 10,
    "size": 20,
    "query": {
        "match": {
            "example-field": "example"
        }
    }
}
```

## Define and use index aliases

### Alias Types

* Data stream alias
* Index Alias

An alias can point to one type OR the other.

### Define an Index alias

wildcard patterns are accepted.

```elasticsearch_console_command
PUT _aliases
{
    "actions" [
        {
            "add": {
                "index": "example*",
                "alias": "examples"
            }
        }
    ]
}
```

```elasticsearch_console_command
PUT _aliases
{
    "actions" [
        {
            "add": {
                "index": "example-1",
                "alias": "main-example"
            }
        }
    ]
}
```

### Remove an Index Alias

```elasticsearch_console_command
PUT _aliases
{
    "actions" [
        {
            "remove": {
                "index": "example-1",
                "alias": "main-example"
            }
        }
    ]
}
```

## Define and use a search template

A search template is a search you can run with different variables

### Define a search template

Uses the Stored Script API.

```elasticsearch_console_command
PUT _scripts/example-search-template-name
{
    "script": {
        "lang": "mustache",
        "source": {
            "query": {
                "match": {
                    "example-field": "{{ example_variable }}"
                }
            },
            "from": "{{from}}",
            "size": "{{size}}"
        },
        "params": {
            "query_string": "example",
            "from": 0,
            "size": 10
        }
    }
}
```

### Validate a search template

```elasticsearch_console_command
POST _render/template
{
    "id": "example-search-template-name",
    "params": {
        "query_string": "example",
        "from": 15,
        "size": 5
    }
}
```

### Execute a templated search

```elasticsearch_console_command
{
    "id": "example-search-template-name",
    "params": {
        "query_string": "example",
        "from": 15,
        "size": 5
    }
}
```
