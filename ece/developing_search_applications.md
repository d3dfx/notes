# Developing Search Applications

## Highlight the search terms in the response of a query

Default tags used for highlighting are `<em></em>`

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

## Implement pagination of the results of a search query

## Define and use index aliases

## Define and use a search template

