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

## Define and use a search template

