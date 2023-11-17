# Data Processing

## Define a mapping that satisfies a given set of requirements

Fields that are already mapped can not be updated but new fields can be added.

### Create Index API Definition

| Endpoint        | Method | Description     |
| --------------- | ------ | --------------- |
| /\<index_name\> | PUT    | Create an Index |

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

| Endpoint                  | Method | Description              |
| ------------------------- | ------ | ------------------------ |
| /\<index_name\>/\_mapping | PUT    | Update an Index mappings |

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

| Endpoint                   | Method | Description           |
| -------------------------- | ------ | --------------------- |
| /\<index_name\>/\_settings | PUT    | Update Index Settings |

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

- https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-custom-analyzer.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/specify-analyzer.html

## Define and use multi-fields with different data types and/or analyzers

Multi-fields allows a field to be indexed multiple times.

#### Example API Call to create a multi-field mapping

```
PUT example-index
{
    "mappings": {
        "properties":{
            "fullname": {
                "type": "keyword",
                "fields":{
                    "as_standard":{
                        "type": "text"
                    },
                    "as_english": {
                        "type": "text",
                        "analyzer": "english"
                    },
                    "as_whitespace": {
                        "type": "text",
                        "analyzer": "whitespace"
                    }
                }
            }
        }
    }
}
```

- https://www.elastic.co/guide/en/elasticsearch/reference/8.11/multi-fields.html

## Use the Reindex API and Update By Query API to reindex and/or update documents

### Reindex API Definition

| Endpoint  | Method | Description                                               |
| --------- | ------ | --------------------------------------------------------- |
| \_reindex | POST   | Copy documents from a source index to a destination index |

#### Request Body Schema

```
{
    "conflicts": "proceed || abort",
    "max_docs": "INTEGER",
    "source": {
        "index": "STRING || []",
        "query": {},
        "remote": {},
        "size": "INTEGER",
        "slice": {},
        "_source": "true || []"
        "dest": {},
        "script": {}
    }
}
```

#### Example Call

```
POST _reindex
{
  "source": {
    "index": "my-example-source-index"
  },
  "dest": {
    "index": "my-example-destination-index"
  }
}
```

#### Example Call to update docs while reindexing

```
POST _reindex
{
    "source": {
        "index": "my-example-source-index"
    },
    "dest": {
        "index": "my-example-destination-index"
    },
    "script": {
        "lang": "painless",
        "source": "if ( ctx._source.foo == 'bar' ) {ctx._version++; ctx._source.remove('foo')}"
    }
}
```

### Update by Query API Definition

Updates documents that match the specified query. If no query is specified, performs an update on every document in the data stream or index without modifying the source, which is useful for picking up mapping changes.

| Endpoint                          | Method | Description                                       |
| --------------------------------- | ------ | ------------------------------------------------- |
| /\<index_name\>/\_update_by_query | POST   | Updates documents that match the specified query. |

#### Example Call

{
"query":{
"term": {
"foo": "bar"
}
},
"script": {
"lang": "painless",
"source": "ctx.\_source.remove('foo')"
}
}

## Define and use an ingest pipeline that satisfies a given set of requirements, including the use of Painless to modify documents

### Assign Lifecycle Management Policy to an index API Definition

| Endpoint                             | Method | Description               |
| ------------------------------------ | ------ | ------------------------- |
| /\_ingest/pipeline/\<pipeline-name\> | PUT    | Create an ingest pipeline |

#### Request Body Schema

```
{
    "description": "STRING",
    "on_failure": [{}],
    "processors": [{}],
    "version": "INTEGER",
    "_meta": {}
}
```

#### Example Call

```
PUT /_ingest/pipeline/example-ingest-pipeline
{
    "description": "Add Foo : bar object",
    "processors": [
        {
            "set": {
                "field": "foo",
                "value": "bar"
            }
        }
    ]
}
```

#### Example Call using the Script Processor

```
PUT /_ingest/pipeline/example-ingest-pipeline
{
    "description": "Add Foo : bar object",
    "processors": [
        {
            "script": {
                "lang": "painless",
                "source": "ctx.foo = 'bar'"
            }
        }
    ]
}
```

### Use a pipeline with an indexing request

This can be done with the following apis

- \_doc
- \_bulk
- \_update_by_query
- \_reindex

```
POST example-index/_doc?pipeline=<pipeline-name>
POST example-index/_bulk?pipeline=<pipeline-name>
POST example-index/_update_by_query?pipeline=<pipeline-name>
POST example-index/_reindex?pipeline=<pipeline-name>
```

### Set a default pipeline

This is set in the index settings

```
PUT example-index/_settings
{
    "index" {
        "default_pipeline": "<pipeline-name>"
    }
}
```

## Define runtime fields to retrieve custom values using Painless scripting

### Define a Runtime Field

| Endpoint         | Method | Description     |
| ---------------- | ------ | --------------- |
| /\<index_name\>/ | PUT    | Create an Index |

#### Request Body Schema

```
{
    "mappings": {
        "runtime": {
            "day_of_week": {
                "type": "keyword",
                "script": {
                    "source": "emit()",
                    "lang": "painless",
                    "params": {}
                }
            }
        }
    }
}
```

#### Example Call

```
PUT new-index1
{
    "mappings": {
        "runtime": {
            "day_of_week": {
                "type": "keyword",
                "script": {
                    "source": "emit(doc['@timestamp'].value.dayOfWeekEnum.getDisplayName(TextStyle.FULL, Locale.ROOT))"
                }
            }
        },
        "properties": {
            "@timestamp" {
                "type": "date"
            }
        }
    }
}
```

- https://www.elastic.co/guide/en/elasticsearch/reference/current/runtime.html
