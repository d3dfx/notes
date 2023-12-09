# Review actions done in the lab

```elasticsearch_console_commands
PUT covid-data
{
  "aliases": {
    "covid-death": {}
  }, 
  "mappings": {
    "properties": {
      "data_as_of": {
        "type": "date"
      },
      "start_date": {
        "type": "date"
      },
      "end_date": {
        "type": "date"
      },
      "sex": {
        "type": "text"
      },
      "age_years": {
        "type": "text"
      },
      "total_deaths": {
        "type": "integer"
      },
      "covid19_deaths": {
        "type": "integer"
      }
    }
  },
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  }
}

GET _cat/indices?v

GET covid-death/_mapping

PUT _component_template/covid-mappings
{
  "_meta": {
    "description": "This shit defines common fields amongst covid data"
  },
  "template": {
    "mappings": {
      "runtime": {
        "date_range": {
          "type": "keyword",
          "script": {
          "source": "emit( doc['start_date'].value + ' - ' + doc['end_date'].value)"
          }
        }
      },
      "properties": {
        "sex": {
          "type": "text"
        },
        "age_years": {
          "type": "text"
        },
        "total_deaths": {
          "type": "integer"
        },
        "covid19_deaths": {
          "type": "integer"
        }
      },
      "dynamic_templates": [
        {
          "long-to-integer": {
            "match_mapping_type": "long",
            "mapping":{
              "type": "integer"
            }
          }
        }
      ]
    }
  }
}

POST _scripts/painless/_execute
{
  "script": {
    "source": """
    for (i  in params.a ) {
      String b = i;
    };
    return b;
    """,
    "params": {
      "a": "test"
    }
  }
}

GET _component_template/covid-mappings

PUT _index_template/covid-template
{
  "index_patterns": ["covid-*"],
  "composed_of": ["covid-mappings"],
  "template":{
    "settings": {
      "number_of_replicas": 1,
      "index.lifecycle.name": "covid-lifecycle"
    }
  }
}

GET _index_template/covid-template

GET covid-data/_search

GET test-index03

PUT _ilm/policy/covid-lifecycle
{
  "policy": {
    "phases": {
      "warm": {
        "min_age": "10d",
        "actions": {
          "shrink": {
            "number_of_shards": 1
        }
        }
      },
      "delete": {
        "min_age": "30d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}

GET _ilm/policy/covid-lifecycle

PUT _index_template/example-datastream-tpl
{
  "data_stream": {},
  "index_patterns": ["example-ds-*"],
  "priority": 500,
  "template": {
    "mappings" : {
      "properties": {
        "@timestamp": { 
          "type": "date"
        }
      }
    }
  }
}

GET _index_template/example-datastream-tpl

PUT _index_template/example-analyzer-tpl
{
  "index_patterns": ["example-analyzer*"],
  "template": {
    "settings": {
      "analysis": {
        "analyzer": {
          "example-custom-analyzer": {
            "type": "custom",
            "tokenizer": "standard",
            "char_filter": [
              "filter_gender"
            ],
            "filter": [
              "asciifolding",
              "trim"
            ]
          }
        },
        "char_filter": {
          "filter_gender": {
            "type": "mapping",
            "mappings": [
              "Female => F",
              "Male => M"
            ]
          }
        }
      }
    }
  }
}

GET _index_template/example-analyzer-tpl

PUT _index_template/example-default-analyzer-tpl
{
  "index_patterns": ["example-default-analyzer*"],
  "template": {
    "settings": {
      "analysis":{
        "analyzer": {
          "default": {
            "type": "custom",
            "tokenizer": "standard",
            "filter": [
              "trim"
            ],
            "char_filter": [
              "space_to_underscore"
            ]
          }
        },
        "char_filter": {
          "space_to_underscore": {
            "type": "pattern_replace",
            "pattern": "\\s+",
            "replacement": "_"
          }
        }
      }
    }
  }
}

GET _index_template/example-default-analyzer-tpl

PUT _index_template/example-multi-field-tpl
{
  "index_patterns": ["example-multi-field*"],
  "template": {
    "mappings": {
      "properties": {
        "fullname": {
          "type": "keyword",
          "fields": {
            "standard_analysis": {
              "type": "text"
            },
            "english_analysis": {
              "type": "text",
              "analyzer": "english"
            },
            "simple_analysis": {
              "type": "text",
              "analyzer": "simple"
            }
          }
        }
      }
    }
  }
}

GET _index_template/example-multi-field-tpl 

POST _reindex
{
  "source": {
    "index": "covid-death"
  },
  "dest": {
    "index": "covid-reindex-example"
  }
}

GET covid-data/_search?size=100
{
  "query": {
    "wildcard": {
      "age_years": {
        "value": "0*"
      }
    }
  }
}

POST _reindex
{
  "source": {
    "index": "covid-death",
    "query": {
      "match": {
        "age_years": "85"
      }
    }
  },
  "dest": {
    "index": "covid-85-and-older"
  },
  "script": {
    "lang": "painless",
    "source": """
    if( ctx._source.sex == 'Female') {ctx._source.sex = 'F'}
    if( ctx._source.sex == 'Male') {ctx._source.sex = 'M'}
    """
  }
}

GET covid-85-and-older/_search

GET covid-reindex-example/_search

{
  "query": {
    "match_all": {}
  }
}

DELETE covid-reindex-example

POST covid-reindex-example/_update_by_query
{
  "query": {
    "match_all": {}
  },
  "script": {
    "source": """
    ctx._source.total_deaths = Integer.parseInt(ctx._source.total_deaths)
    """,
    "lang": "painless"
  }
}

PUT _ingest/pipeline/example-covid-pipeline
{
  "description": "normalize covid data",
  "processors": [
    {
      "convert": {
        "field": "total_deaths",
        "type": "integer"
      }
    },
    {
      "script": {
        "lang": "painless",
        "source": """
          ctx.sex = (String)ctx.sex.toUpperCase().charAt(0);
        """
      }
    }
  ]
}

GET _ingest/pipeline/example-covid-pipeline

POST _scripts/painless/_execute
{
  "script": {
    "source": """
    params.t.toUpperCase().charAt(0);
    """,
    "params": {
      "t": "test"
    }
  }
}

GET _ingest/pipeline/example-covid-pipeline

POST _reindex
{
  "source": {
    "index": "covid-death"
  },
  "dest": {
    "index": "covid-after-pipeline",
    "pipeline": "example-covid-pipeline"
  }
}

GET covid-data/_alias

DELETE covid-data/_aliases/test-alias

{
  "test-alias": null
}

GET covid-death/_search

GET covid-data/_mapping


GET covid-after-pipeline/_search/template

GET broken-covid/_search

POST _reindex
{
  "source": {
    "index": "covid-data"
  },
    "dest": {
      "index": "covid-test"
    }
}

GET covid-test/_search
{
  "fields": [
    "covid19_deaths",
    "total_deaths", 
    "date_range"
  ]
}

GET covid-test/_mapping

DELETE covid-test

GET _cat/indices?v

GET _cat/indices/break-test?v

GET _cat/shards/break-test?v

GET _cluster/allocation/explain
{
  "index": "break-test",
  "primary": "true",
  "shard": 0
}

GET _cat/nodeattrs?v

GET _cat/nodes?v

PUT break-test/_settings
{
  "index.routing.allocation.exclude._name": "es01,es02,es03"
}

GET break-test/_settings

POST _reindex
{
  "source": {
    "index": "covid-data"
  },
  "dest": {
    "index": "break-test"
  }
}

PUT break-test
{
  "settings": {
    "number_of_replicas": 0,
    "number_of_shards": 1,
    "index.routing.allocation.exclude._name": "es01,es02,es03"
  }
}

DELETE break-test


PUT _snapshot/local
{
  "type": "fs",
  "settings": {
    "location": "/usr/share/elasticsearch/backup"
  }
}

PUT _snapshot/local/new-snap
{
  "indices": "covid-data"
}

POST _snapshot/local/new-snap/_restore
{
  "indices": "covid-data"
}

DELETE covid-data

GET covid-data/_search

POST _snapshot/local/new-snap/_mount
{
  "index": "covid-data",
  "renamed_index": "mounted-covid"
}

GET mounted-covid/_search

DELETE mounted-covid

GET _cluster/settings

GET _remote/info

GET accounts/_mapping

GET accounts/_search

GET accounts/_search
{
  "query":{
    "match": {
      "employer": "Pyrami"
    }
  }
}

GET accounts/_search
{
  "query": {
    "match_phrase": {
      "address": "880 Holmes Lane"
    }
  }
}

GET accounts/_search
{
  "query": {
    "multi_match":{
      "query": "Amber",
      "fields": ["email","firstname"]
    }
  }
}

GET accounts/_search
{
  "query": {
    "query_string": {
      "default_field": "employer",
      "query": "Netagy OR Pyrami"
    }
  }
}

GET accounts/_search
{
  "query": {
    "term": {
      "firstname": "Amber"
    }
  }
}

GET covid-data/_search

GET covid-data/_mapping

GET covid-data/_search
{
  "aggs":{
    "gender": {
      "terms": {
        "field": "sex"
      }
    }
  }
}

GET accounts/_mapping

GET accounts/_search

PUT test
{
  "mappings": {
    "properties": {
      "test": {
        "type": "text",
        "analyzer": "test-analyzer"
      }
    }
  },
  "settings": {
    "analysis": {
      "analyzer": {
        "test-analyzer": {
          "type": "custom",
          "tokenizer": "keyword",
          "filter": [
            "yoo-synonym"
          ]
        }
      },
      "filter": {
        "yoo-synonym": {
          "type": "synonym_graph",
          "lenient": true,
          "synonyms": ["yoo-hoo, yoohoo"]
        }
      }
    }
  }
}

POST test/_doc
{
  "test": "yoo-hoo"
}

GET test/_analyze
{
  "text": "yoo-hoo",
  "field": "test"
}

GET _analyze
{
  "text": "yoo-hoo",
  "tokenizer": "keyword",
  "filter": [
    {
      "type": "synonym",
      "synonyms": ["yoo-hoo, yoohoo, yoo hoo"]
    }
  ]
}

GET test/_search
{
  "query": {
    "match": {
      "test": "yoohoo"
    }
  }
}

GET test/_mapping

DELETE test 

GET accounts/_search

PUT _ingest/pipeline/account-field-removal
{
  "processors": [
    {
      "remove": {
        "field": "employer"
      }
    }
  ]
}
 
POST _reindex
{
  "source": {
    "index": "accounts"
  },
  "dest": {
    "index": "accounts-with-missing-fields",
    "pipeline": "account-field-removal"
  }
}

GET accounts-with-missing-fields/_search

PUT _enrich/policy/accounts-add-missing-fields
{
  "match": {
    "indices": "accounts",
    "match_field": "email",
    "enrich_fields": ["employer"]
  }
}

POST _enrich/policy/accounts-add-missing-fields/_execute

PUT _ingest/pipeline/accounts-add-missing-fields
{
  "processors": [
    {
      "enrich": {
        "policy_name": "accounts-add-missing-fields",
        "field": "email",
        "target_field": "enrich"
      }
    },
    {
      "set": {
        "field": "employer",
        "copy_from": "enrich.employer"
      }
    },
    {
      "remove": {
        "field": "enrich"
      }
    }
  ]
}

POST _reindex
{
  "source": {
    "index": "accounts-with-missing-fields"
  },
  "dest": {
    "index": "fixed-accounts-index",
    "pipeline": "accounts-add-missing-fields"
  }
}

GET fixed-accounts-index/_search
  
DELETE fixed-accounts-index

GET accounts/_search

GET accounts/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "gender": "M"
          }
        },
        {
          "prefix": {
            "lastname": {
              "value": "H"
            }
          }
        }
      ],
      "boost": 2
    }
  }
}
```
