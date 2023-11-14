# Data Processing

## Define a mapping that satisfies a given set of requirements
Fields that are already mapped can not be updated but new fields can be added.

### Create Index API Definition
| Endpoint | Method | Description | 
|----------|--------|-------------|
| /\<index\_name\> |  PUT   | Create an Index|

#### Example Call
```
PUT new-index1
{
    "mappings": {
        "properties": {
            "age": {
                "type": "integer"
            }
        }
    }
}
```

### Update Index Field Mappings API Definition
| Endpoint | Method | Description | 
|----------|--------|-------------|
| /\<index\_name\>/_mapping |  PUT   | Update an Index mappings|

#### Example Call
```
PUT new-index1/_mapping
{
    "properties": {
        "first-name": {
            "type": "keyword"
        },
        "last-name": {
            "type": "keyword"
        }
    }
}
```
## Define and use a custom analyzer that satisfies a given set of requirments
Defined under index settings and can be defined at the index level and the field level.

## Create a custome analyzer API Definition
| Endpoint | Method | Description | 
|----------|--------|-------------|
| /\<index\_name\>/\_settings | PUT | Update Index Settings |

#### Example Call
```
PUT new-index1
{
    "settings":{
        "analysis" : {
            "analyzer": {
                "example-custom-analyzer-name":{
                    "type": "custom",
                    "tokenizer": "standard",
                    "char_filter": [
                        "html_strip"
                    ],
                    "filter": [
                        "lowercase",
                        "asciifolding
                    ]
                }
            }
        }
    }
}
```

#### Example Call to Assign a default analyzer
```
PUT new-index1
{
    "settings": {
        "analysis" : {
            "analyzer": {
                "default":{
                    "type": "custom",
                    "tokenizer": "standard",
                    "char_filter": [
                        "html_strip"
                    ],
                    "filter": [
                        "lowercase",
                        "asciifolding
                    ]
                }
            }
        }
    }
}
```

* https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-custom-analyzer.html
* https://www.elastic.co/guide/en/elasticsearch/reference/current/specify-analyzer.html

## Define and use multi-fields with different data types and/or analyzers

## Use the Reindex API and Update By Query API to reindex and/or update documents

## Define and use an ingest pipeline that satisfies a given set of requirements, including the use of Painless to modify documents

## Define runtime fields to retrieve custom values using Painless scripting

