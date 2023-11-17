# define indexes
This file documents the Elastic Console cmds apart of the elastic certified engineer exam prepbook

## Define an index with default properties
PUT demo-01

## Define an index for the shakespeare example
```
PUT shakespeare
{
	"settings":{
		"number_of_replicas":1,
		"number_of_shards":1
	},
	"mappings": {
        "properties":{
            "speaker": {
                "type": "keyword"
            },
            "play_name":{
                "type": "keyword"
            },
            "line_id": {
                "type":"integer"
            },
            "speech_number": {
                "type": "integer"
            }
        }
    }
}
```
### Add Data to the shakespeare index
```
curl -k -u elastic:elastic \
    -H'Content-Type:application/xndjson' \
    -X POST \
    'https://localhost:9200/shakespeare/_bulk?pretty' \
    --data-binary @shakespeare.json
```

## Create Index Templates
```
PUT /_index_template/apachelogs
{
    "index_patterns" : [
        "apachelogs*"
    ],

    "priority" : 501,
    "template" : {
        "settings" : {
            "number_of_shards" : "1",
            "number_of_replicas" : "1"
        },
        "mappings" : {
            "properties" : {
                "@timestamp" : {
                "type" : "date"
                },
                "coordinates" : {
                "type" : "geo_point"
                }
            }
        }
    }
}
```

### Create Component Template
```
PUT /_component_template/apachelogs_settings_mappings
{
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 1
    },
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        },
        "coordinates": {
          "type": "geo_point"
        }
      }
    }
  }
}
```
#### Create Index Template that uses the Component Template
```
PUT /_index_template/apachelogs2
{
  "index_patterns":[
    "demo*"
  ],
  "priority": 501,
  "composed_of": ["apachelogs_settings_mappings"]
}
```