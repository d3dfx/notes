# george bridgeman completed labs

[Link to the Labs](https://georgebridgeman.com/exercises/)

```elasticsearch_console_commands
GET _cat/indices/olympic-events?v&h=index,health,docs.count,pri.store.size

GET _cat/shards/olympic-events?v

GET _cat/indices/covid-test?v

GET _cluster/allocation/explain
{
  "index": "covid-test",
  "shard": 0,
  "primary": "true"
}

GET covid-test/_settings

PUT covid-test/_settings
{
  "index.routing.allocation.require.test": null
}

GET olympic-events/_search?size=0
{
  "aggs": {
    "age_bucket": {
      "terms": {
        "field": "Age"
      }
    }
  }
}

POST _reindex 
{
  "source": {
    "index": "olympic-events"
  },
  "dest": {
    "index": "olympic-events-backup"
  }
}

GET olympic-events-backup/_search

GET olympic-events/_search
{
  "query": {
    "multi_match": {
      "query": "NA",
      "fields": ["Age","Height","Weight"]
    }
  },
  "fields": [
    "Age",
    "Height",
    "Weight"
  ]
}

POST olympic-events/_delete_by_query
{
  "query": {
    "multi_match": {
      "query": "NA",
      "fields": ["Age","Height","Weight"]
    }
  }
}

GET olympic-events/_search
{
  "query": {
    "regexp": {
      "Games": ".*Winter"
    }
  }
}

GET olympic-events/_search
{
  "query": { 
    "regexp": {
      "Games": {
        "value": "\\d{4,4}.*"
      }
    }
  }
}

GET _ingest/processor/grok

PUT _ingest/pipeline/split_games
{
  "processors": [
    {
      "grok": {
        "field": "Games",
        "patterns": ["%{YEAR:year} %{SEASON:season}"],
        "pattern_definitions": {
          "SEASON": "[S,s]ummer|[F,f]all|[W,w]inter|[S,s]pring"
        }
      }
    },
    {
      "remove": {
        "field": "Games"
      }
    }
  ]
}

GET _ingest/pipeline/split_games/_simulate
{
  "docs": [
      {
        "_index" : "olympic-events",
        "_id" : "79e6MYwBBaD6PYyD9iaq",
        "_score" : 1.0,
        "_source" : {
          "NOC" : "ARG",
          "Sex" : "M",
          "City" : "Los Angeles",
          "Weight" : "98",
          "Name" : "Ernesto Arturo Alas",
          "Sport" : "Shooting",
          "Games" : "1984 Summer",
          "Event" : "Shooting Men's Free Pistol, 50 metres",
          "Height" : "186",
          "Team" : "Argentina",
          "ID" : 2224,
          "Medal" : "NA",
          "Age" : "54"
        }
      },
            {
        "_index" : "olympic-events",
        "_id" : "Ite6MYwBBaD6PYyD9ier",
        "_score" : 1.0,
        "_source" : {
          "NOC" : "TUR",
          "Sex" : "M",
          "City" : "Grenoble",
          "Weight" : "68",
          "Name" : "Burhan Alanku",
          "Sport" : "Alpine Skiing",
          "Games" : "1968 Winter",
          "Event" : "Alpine Skiing Men's Slalom",
          "Height" : "170",
          "Team" : "Turkey",
          "ID" : 2254,
          "Medal" : "NA",
          "Age" : "17"
        }
      }
  ]
}

PUT olympic-events-fixed
{
  "settings": {
    "number_of_replicas": 0,
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "athleteId": {
        "type": "integer"
      },
      "age": {
        "type": "short"
      },
      "height": {
        "type": "short"
      },
      "weight": {
        "type": "short"
      },
      "athleteName": {
        "type": "text",
        "fields": {
          "raw": {
            "type": "keyword"
          }
        }
      },
      "gender": {
        "type": "keyword"
      },
      "team": {
        "type": "keyword"
      },
      "noc": {
        "type": "keyword"
      },
      "year": {
        "type": "short"
      },
      "season": {
        "type": "keyword"
      },
      "city": {
        "type": "text",
        "fields": {
          "raw": {
            "type": "keyword"
          }
        }
      },
      "sport": {
        "type": "keyword"
      },
      "event": {
        "type": "text",
        "fields": {
          "raw": {
            "type": "keyword"
          }
        }
      },
      "medal": {
        "type": "keyword"
      }
    }
  }
}

GET olympic-events-fixed/_mapping

POST _reindex
{
  "source": {
    "index": "olympic-events"
  },
  "dest": {
    "index": "olympic-events-fixed",
    "pipeline": "split_games"
  }
}

GET olympic-events-fixed/_search

PUT _index_template/olympic-events
{
  "index_patterns": ["olympic-events-*"],
  "template": {
   "settings": {
      "number_of_replicas": 0,
      "number_of_shards": 1
    },
    "mappings": {
      "dynamic": "false",
      "properties": {
        "athleteId": {
          "type": "integer"
        },
        "age": {
          "type": "short"
        },
        "height": {
          "type": "short"
        },
        "weight": {
          "type": "short"
        },
        "athleteName": {
          "type": "text",
          "fields": {
            "raw": {
              "type": "keyword"
            }
          }
        },
        "gender": {
          "type": "keyword"
        },
        "team": {
          "type": "keyword"
        },
        "noc": {
          "type": "keyword"
        },
        "year": {
          "type": "short"
        },
        "season": {
          "type": "keyword"
        },
        "city": {
          "type": "text",
          "fields": {
            "raw": {
              "type": "keyword"
            }
          }
        },
        "sport": {
          "type": "keyword"
        },
        "event": {
          "type": "text",
          "fields": {
            "raw": {
              "type": "keyword"
            }
          }
        },
        "medal": {
          "type": "keyword"
        }
      }
    }
  }
}

PUT _ingest/pipeline/reconcile_fields
{
  "processors": [
    {
      "rename": {
        "field": "Age",
        "target_field": "age"
      }
    },
    {
      "rename": {
        "field": "ID",
        "target_field": "athleteId"
      }
    },
    {
      "rename": {
        "field": "Height",
        "target_field": "height"
      }
    },
    {
      "rename": {
        "field": "Weight",
        "target_field": "weight"
      }
    },
    {
      "rename": {
        "field": "Name",
        "target_field": "athleteName"
      }
    },
    {
      "rename": {
        "field": "Sex",
        "target_field": "gender"
      }
    },
    {
      "rename": {
        "field": "Team",
        "target_field": "team"
      }
    },
    {
      "rename": {
        "field": "NOC",
        "target_field": "noc"
      }
    },
    {
      "rename": {
        "field": "City",
        "target_field": "city"
      }
    },
    {
      "rename": {
        "field": "Sport",
        "target_field": "sport"
      }
    },
    {
      "rename": {
        "field": "Event",
        "target_field": "event"
      }
    },
    {
      "rename": {
        "field": "Medal",
        "target_field": "medal"
      }
    },
    {
      "pipeline": {
        "name": "split_games"
      }
    }
  ]
}

GET _ingest/pipeline/reconcile_fields/_simulate
{
  "docs": [
    {
      "_source": {
        "NOC": "ARG",
        "Sex": "M",
        "City": "Los Angeles",
        "Weight": "98",
        "Name": "Ernesto Arturo Alas",
        "Sport": "Shooting",
        "Games": "1984 Summer",
        "Event": "Shooting Men's Free Pistol, 50 metres",
        "Height": "186",
        "Team": "Argentina",
        "ID": 2224,
        "Medal": "NA",
        "Age": "54"
      }
    }
  ]
}

DELETE olympic-events-fixed

POST _reindex
{
  "source": {
    "index": "olympic-events"
  },
  "dest": {
    "index": "olympic-events-fixed",
    "pipeline": "reconcile_fields"
  }
}

GET olympic-events-fixed/_search

GET olympic-events-fixed/_search?size=0
{
  "query": {
    "match": {
      "sport": "Gymnastics"
    }
  },
  "aggs": {
    "unique-gymnastic-events": {
      "terms": {
        "field": "event.raw"
      }
    }
  }
}

GET olympic-events-fixed/_search
{
  "query": {
    "match_phrase": {
      "sport": "Gymnastics"
    }
  },
  "size": 0,
  "aggs": {
    "avg-weight-based-on-gender": {
      "terms": {
        "field": "gender"
      },
      "aggs": {
        "avg-weight": {
          "avg": {
            "field": "weight"
          }
        }
      }
    }
  }
}

POST olympic-events-fixed/_search
{
  "size": 0,
  "aggs": {
    "event-debut-year": {
      "terms": {
        "field": "event.raw"
      },
      "aggs": {
        "debut-year": {
          "min": {
            "field": "year"
          }
        },
        "sort-by-year": {
          "bucket_sort": {
            "sort": [
              {
                "debut-year": {
                  "order": "desc"
                }
              }
            ]
          }
        }
      }
    }
  }
}

GET olympic-events-fixed/_search
{
  "size": 0,
  "aggs": {
    "event-debut-year": {
      "terms": {
        "field": "event.raw"
      },
      "aggs": {
        "debut-year": {
          "min": {
            "field": "year"
          }
        },
        "sort-by-year": {
          "bucket_sort": {
            "sort": [
              {
                "debut-year": {
                  "order": "desc"
                }
              }
            ]
          }
        }
      }
    },
    "most-recent": {
      "max_bucket": {
        "buckets_path": "event-debut-year>debut-year"
      }
    }
  }
}

GET olympic-events-fixed/_search
{
  "query":{
    "bool": {
      "must": [
        {
          "match_phrase": {
            "year": "2016"
          }
        },
        {
          "match_phrase": {
            "city": "Rio de Janeiro"
          }
        }
      ]
    }
  },
  "size": 50,
  "_source": [
    "athleteName",
    "team",
    "sport",
    "age",
    "height",
    "weight",
    "gender"
  ], 
  "sort": [
    {
      "height": {
        "order": "desc"
      }
    }
  ]
}

GET olympic-events-fixed/_search
{
  "query":{
    "term": {
      "year": {
        "value": 2016
      }
    }
  },
  "size": 50,
  "_source": [
    "athleteName",
    "team",
    "sport",
    "age",
    "height",
    "weight",
    "gender"
  ]
}

PUT olympic-events-fixed/_mapping
{
  "runtime": {
    "weightLbs": {
      "type": "double",
      "script": {
        "source": "emit(doc['weight'].value*2.2)"
      }
    }
  }
}

GET olympic-events-fixed/_search
{
  "fields": [
    "weightLbs"
  ]
}

PUT olympic-events-fixed/_mapping
{
  "runtime": {
    "bmi": {
      "type": "double",
      "script": {
        "source": "emit( doc['weight'].value / (Math.pow((doc['height'].value / 100.0),2) ))"
      }
    }
  }
}

GET olympic-events-fixed/_search
{
  "fields": [
    "bmi"
  ]
}

GET olympic-events-fixed/_search
{
  "query": {
    "match": {
      "medal": "Gold"
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ],
  "size": 50
}

GET olympic-events-fixed/_search
{
  "query": {
    "term": {
      "medal": {
        "value": "Gold"
      }
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ],
  "size": 50
}


GET olympic-events-fixed/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "range": {
            "weight": {
              "_name": "weight",
              "gte": 60,
              "lte": 70
            }
          }
        },
        {
          "range": {
            "age": {
              "_name": "age",
              "lt": 20
            }
          }
        }
      ],
      "minimum_should_match": 1
    }
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ],
  "highlight": {
    "pre_tags": ["<b>"],
    "post_tags": ["</b>"], 
    "fields": {
      "weight": {},
      "age": {}
    }
  }
}

GET olympic-noc-regions/_search

PUT _enrich/policy/olympic-noc-append
{
  "match": {
    "indices": "olympic-noc-regions",
    "match_field": "noc",
    "enrich_fields": ["region"]
  }
}

PUT _enrich/policy/olympic-noc-append/_execute

PUT _ingest/pipeline/enrich-noc
{
  "processors": [
    {
      "enrich": {
        "policy_name": "olympic-noc-append",
        "field": "noc",
        "target_field": "nocDetails"
      }
    }
  ]
}

PUT olympic-events-enriched
{
  "mappings": {
    "dynamic": true
  }
}

POST _reindex
{
  "source": {
    "index": "olympic-events-fixed"
  },
  "dest": {
    "index": "olympic-events-enriched",
    "pipeline": "enrich-noc"
  }
}

GET olympic-events-enriched/_search

GET _cat/indices/olympic-noc-regions?v
```
