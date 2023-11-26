# Data Management

## Define an index that satisfies a given set of requirements

### Create Index API Definition

| Endpoint        | Method | Description     |
| --------------- | ------ | --------------- |
| /\<index_name\> | PUT    | Create an Index |

#### Request Body Schema

```elasticsearch_console_command
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

| Property Name  | Type             | Description                                                                                                                           | Default |
| -------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| filter         | QUERY DSL OBJECT | Define the query to issue when using the alias.                                                                                       | n/a     |
| index_routing  | STRING           | Value used to route indexing operations to a specific shard. If specified, this overwrites the routing value for indexing operations. | n/a     |
| is_hidden      | BOOL             | Makes alias only visible to Admins. All indicies with the alias must be hidden.                                                       | FALSE   |
| is_write_index | BOOL             | Makes this index the write index for the alias                                                                                        | FALSE   |
| routing        | STRING           | Value used to route indexing and search operations to a specific shard.                                                               | n/a     |
| search_routing | STRING           | Value used to route search operations to a specific shard. If specified, this overwrites the routing value for search operations      | n/a     |

#### Mappings Properties

| Property Name          | Type      | Description                                                                                                                                                                                                                                | Default                             |
| ---------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------- |
| Type                   | DATA_TYPE | Explicitly assign the data type                                                                                                                                                                                                            | n/a                                 |
| analyzer               | STRING    | Assign an analyzer for the field                                                                                                                                                                                                           | standard                            |
| coerce                 | BOOL      | Numbers enclosed in quotes will be automatically converted to integers when indexed. i.e. "10" -> 10                                                                                                                                       | TRUE                                |
| copy_to                | STRING    | Copy & Concatenate field values into another field. The Field is not Indexed but is abled to be queried                                                                                                                                    | n/a                                 |
| doc_values             | BOOL      | Enable or disable a field being written to disk. If not written to disk it will be saved & query-able from the Inverse Index.                                                                                                              | FALSE                               |
| dynamic                | BOOL      | Enable dynamic assignment on child properties when dynamic is disabled at the type level.                                                                                                                                                  | TRUE                                |
| eager_global_ordinals  | BOOL      | Allows keeping ordinals loaded after the initial call to load them.                                                                                                                                                                        | TRUE                                |
| enabled                | BOOL      | Makes the field indexable and searchable. If False the field is stored but not searchable.                                                                                                                                                 | TRUE                                |
| format                 | STRING    | Assign a date format Ex. dd-mm-yyyy                                                                                                                                                                                                        | n/a                                 |
| ignore_above           | INTEGER   | Do not index or store strings with a character count longer than the defined number                                                                                                                                                        | n/a                                 |
| ignore_malformed       | BOOL      | Allows fields with incorrect data types to be ingested but they are not indexed                                                                                                                                                            | FALSE                               |
| index                  | BOOL      | Controls whether or not fields are indexed                                                                                                                                                                                                 | TRUE                                |
| index_options          | STRING    | Controls what information is added to the inverted index for search and highlighting purposes                                                                                                                                              | positions                           |
| index_phrases          | BOOL      | If enabled, two-term word combinations (shingles) are indexed into a separate field. This allows exact phrase queries (no slop) to run more efficiently, at the expense of a larger index.                                                 | FALSE                               |
| index_prefixes         | OBJECT    | Enables the indexing of term prefixes to speed up prefix searches.                                                                                                                                                                         | { "min_chars": 2, "max_chars": 20 } |
| meta                   | OBJECT    | This metadata is opaque to Elasticsearch, it is only useful for multiple applications that work on the same indices to share meta information about fields. Ex. units of measurement                                                       | n/a                                 |
| fields                 | OBJECT    | Index the same field in different ways for different purposes.                                                                                                                                                                             | n/a                                 |
| normalizer             | STRING    | Property of keyword fields is similar to analyzer except that it guarantees that the analysis chain produces a single token.                                                                                                               | n/a                                 |
| norms                  | BOOL      | Norms store various normalization factors that are later used at query time in order to compute the score of a document relatively to a query. Norms can take up a lot of disk space. Disable if not needed but they can not be reenabled. | TRUE                                |
| null_value             | STRING    | What string to replace explicitly null values with                                                                                                                                                                                         | n/a                                 |
| position_increment_gap | INTEGER   | When indexing text fields with multiple values a "fake" gap is added between the values to prevent most phrase queries from matching across the values. Modify the gap size with this param.                                               | 100                                 |
| properties             | OBJECT    | Allows sub-fields to be defined                                                                                                                                                                                                            | n/a                                 |
| search_analyzer        | STRING    | Allows selection of an analyzer to use during search time                                                                                                                                                                                  | standard                            |
| similarity             | STRING    | Identify the scoring algorithm for a field                                                                                                                                                                                                 | BM25                                |
| store                  | STRING    | Ensures that the original field value will be available without filtering the \_source field.                                                                                                                                              | FALSE                               |
| subobjects             | BOOL      | Allows dot pathed fields to be converted to objects. Disabling ensures these fields are not converted.                                                                                                                                     | TRUE                                |
| term_vector            | STRING    | Term Vectors contain information about the terms produced by the analysis process. This parameter determines what term vector values are stored.                                                                                           | No                                  |

- <https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html>
- <https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html>
- <https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-params.html>
- <https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules.html>

## Define and use an index template for a given pattern that satisfies a given set of requirements

### Create an Index Template API Definition

| Endpoint                                  | Method | Description              |
| ----------------------------------------- | ------ | ------------------------ |
| /\_index_template/\<index-template-name\> | PUT    | Create an Index Template |

#### Request Body Schema

```elasticsearch_console_command
{
    "composed_of": [],
    "data_stream": {
        "allow_custom_routing": "BOOL",
        "hidden": "BOOL",
        "index_mode": "STRING"
    },
    "index_patterns": [],
    "_meta": {},
    "priority": "INTEGER",
    "template": {
        "aliases": {},
        "mappings": {},
        "settings": {}
    },
    "version": "INTEGER"
}
```

#### Create an Index Template Properties

| Property Name  | Type    | Description                                                                                                                                         | Default |
| -------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| composed_of    | ARRAY   | An ordered list of component template names.                                                                                                        | n/a     |
| data_stream    | OBJECT  | If this object is included, the template is used to create data streams and their backing indices. Supports an empty object.                        | n/a     |
| index_patterns | ARRAY   | Array of wildcard (\*) expressions used to match the names of data streams and indices during creation.                                             | n/a     |
| \_meta         | OBJECT  | Optional user metadata about the index template.                                                                                                    | n/a     |
| priority       | INTEGER | Priority to determine index template precedence when a new data stream or index is created. The index template with the highest priority is chosen. | 0       |
| template       | OBJECT  | Composed of parameters from the Create Index API. Reference Above!                                                                                  | n/a     |
| version        | INTEGER | Version number used to manage index templates externally.                                                                                           | n/a     |

### Create Component Template API Definition

| Endpoint                                        | Method | Description               |
| ----------------------------------------------- | ------ | ------------------------- |
| /\_component_template/<component-template-name> | PUT    | Create Component Template |

#### Request Body Schema

```elasticsearch_console_command
PUT /_component_template/example-component
{
    "template":{
        "aliases": {},
        "mappings": {},
        "settings": {}
    },
    "version": "INTEGER",
    "allow_auto_create": "BOOL",
    "_meta": "STRING"
}
```

## Define and use a dynamic template that satisfies a given set of requirements

### Create Index API Definition

| Endpoint        | Method | Description     |
| --------------- | ------ | --------------- |
| /\<index_name\> | PUT    | Create an Index |

#### Request Body Schema

```elasticsearch_console_command
{
    "mappings": {
        "dynamic_templates": [
            {
                "template_name": {
                    "match_mapping_type": "TYPE",
                    "match": "STRING",
                    "unmatch": "STRING",
                    "mapping": {
                        "type": "TYPE"
                    }
                }
            },
            {
                "template_name2": {
                    "path.match": "parent.child*",
                    "path.unmatch": "*parent.child",
                    "mapping": {
                        "type": "TYPE"
                    }
                }
            }
        ]
    }
}
```

#### Example Call

```elasticsearch_console_command
PUT new-index1
{
    "mappings": {
        "dynamic_templates": [
            {
                "longs_to_strings": {
                    "match_mapping_type": "string",
                    "match": "long_*",
                    "unmatch": "*_text",
                    "mapping": {
                        "type": "TYPE"
                    }
                }
            }
        ]
    }
}
```

## Define an Index Lifecycle Management policy for a time-series index

### Create Index Lifecycle Management Policy API Definition

| Endpoint                                  | Method | Description                                           |
| ----------------------------------------- | ------ | ----------------------------------------------------- |
| /ilm/policy/\<ilm_policy_identifier\> | PUT    | Create or Update an Index Lifecycle Management Policy |

#### Request Body Schema

```elasticsearch_console_command
{
    "policy": {
        "_meta": {},
        "phases": {
            "hot":{
                "actions":{
                    "set_priority": {
                    "priority": "INTEGER"
                    },
                    "unfollow": {},
                    "rollover": {
                        "max_age": "TIME_UNITS",
                        "max_docs": "INTEGER",
                        "max_size": 'BYTE_UNITS",
                        "max_primary_shard_size": "BYTE_UNITS",
                        "max_primary_shard_docs": "INTEGER",
                        "min_age": "TIME_UNITS",
                        "min_docs": "INTEGER",
                        "min_primary_shard_size": "BYTE_UNITS",
                        "min_primary_shard_docs": "INTEGER",
                    },
                    "readonly": {},
                    "downsample": {
                        "fixed_interval": "STRING"
                    },
                    "shrink" : {
                        "number_of_shards": "INTEGER",
                        "max_primary_shard_size": "BYTE_UNITS"
                    },
                    "forcemerge": {
                        "max_num_segments": "INTEGER",
                        "index_codec": "STRING"
                    },
                    "searchable_snapshot": {
                        "snapshot_repository": "STRING",
                        "force_merge_index": "BOOL",
                    }
                }
            },
            "warm": {
                "min_age": "TIME_UNITS",
                "actions":{
                    "set_priority": {
                    "priority": "INTEGER"
                    },
                    "unfollow": {},
                    "readonly": {},
                    "downsample": {
                        "fixed_interval": "STRING"
                    },
                    "shrink" : {
                        "number_of_shards": "INTEGER",
                        "max_primary_shard_size": "BYTE_UNITS"
                    },
                    "forcemerge": {
                        "max_num_segments": "INTEGER",
                        "index_codec": "STRING"
                    },
                }
            },
            "cold":{
                "min_age": "TIME_UNITS",
                "actions":{
                    "set_priority": {
                    "priority": "INTEGER"
                    },
                    "unfollow": {},
                    "readonly": {},
                    "downsample": {
                        "fixed_interval": "STRING"
                    },
                    "searchable_snapshot": {
                        "snapshot_repository": "STRING",
                        "force_merge_index": "BOOL",
                    },
                    "allocate": {
                        "number_of replicas": "INTEGER",
                        "total_shards_per_node": "INTEGER",
                        "include": {},
                        "exclude": {},
                        "require": {}
                    },
                    "migrate": {
                        "enabled": "BOOL"
                    }
                }
            },
            "frozen":{
                "min_age": "TIME_UNITS",
                "actions":{
                    "unfollow": {},
                    "searchable_snapshot": {
                        "snapshot_repository": "STRING",
                        "force_merge_index": "BOOL",
                    }
                }
            }
            {
                "delete": {
                    "min_age": "TIME_UNITS",
                    "actions": {
                        "delete": {
                            "delete_searchable_snapshot": "BOOL"
                        },
                        "wait_for_snapshot": {
                            "policy": "STRING"
                        }
                    }
                }
            }
        }
    }
}
```

#### Example Call

```elasticsearch_console_command
PUT _ilm/policy/example-lifecycle-policy
{
    "policy": {
        "_meta": {},
        "phases": {
            "warm": {
                "min_age": "10d",
                "actions": {
                    "forcemerge": {
                        "max_num_segements": 1
                    }
                }
            },
            {
                "delete": {
                    "min_age": "30d",
                    "actions": {
                        "delete": {}
                    }
                }
            }
        }
    }
}
```

### Assign Lifecycle Management Policy to an index API Definition

| Endpoint                   | Method | Description                   |
| -------------------------- | ------ | ----------------------------- |
| /\<index_name\>/\_settings | PUT    | Update Dynamic Index Settings |

#### Request Body Schema

```elasticsearch_console_command
{
   "index" : {
        "lifecycle": {
            "name" : "STRING",
            "indexing_complete": "BOOL",
            "origination_date": "LONG",
            "step": {
                "wait_time_threshold": "TIME_VALUE"
            },
            "rollover_alias": "STRING"
        }
   }
}
```

#### Example Call

```elasticsearch_console_command
PUT new-index1/_settings
{
   "index" : {
        "lifecycle": {
            "name" : "example-lifecycle",
        }
   }
}
```

- <https://www.elastic.co/guide/en/elasticsearch/reference/current/ilm-settings.html>

## Define an index template that creates a new data stream

Data streams can only be created by index templates. It is recommended to use an ILM policy to manage rollover of data stream indexes.

### Create an Index Template for a Data Stream Index API Definition

| Endpoint                                  | Method | Description              |
| ----------------------------------------- | ------ | ------------------------ |
| /\_index_template/\<index-template-name\> | PUT    | Create an Index Template |

#### Example Call

```elasticsearch_console_command
PUT _index_template/data-stream-example
{
    "data_stream": {},
    "index_patterns": [example-log-stream*],
    "priority": "INTEGER",
    "template": {
        "mappings": {
            "properties": {
                "@timestamp": {
                    "type": "date"
                },
                "message": {
                    "type": "wildcard"
                }
            }
        },
    },
    "version": "1"
}
```
