# Cluster Management

## Diagnose shard issues and repair a cluster's health

### Check Index health

```
# Get all indices
GET _cat/indices

# Get a specific Index's health
GET _cat/indices/<index-name>
```

### Check Shard health

```
# Get all shard health
GET _cat/shards

# Get shard health for a specific index
GET _cat/shards/<index-name>
```

### Check why a shard is allocated the way it is

#### Request Body

```
GET _cluster/allocation/explain
{
    "current_node": "STRING",
    "index": "STRING",
    "primary": "BOOL",
    "shard": "INTEGER"
}
```

#### Example Call

```
# When checking for unassigned shards; an empty request body should be used
GET _cluster/allocation/explain
{}

# Otherwise Identify which shard you need the explanation for
GET _cluster/allocation/explain
{
    "index": "example-index",
    "shard": 0,
    "primary": true
}
```

## Backup and restore a cluster and/or specific indices

### Backup a cluster

Backups are called snapshots in elasticsearch.

#### Configure a Snapshot Repository

| Endpoint                        | Method | Description                                |
| ------------------------------- | ------ | ------------------------------------------ |
| /\_snapshot/\<repository-name\> | PUT    | Create a snapshot repository configuration |

##### Request Body

```
{
    "type": "azure || gcs || s3 || fs || source || url || hdfs",
    "settings": {},
    "verify": "BOOL"
}
```

##### Example

```
PUT /_snapshot/example-file-repository
{
    "type": "fs",
    "settings": {
        "location":"/usr/share/elasticsearch/backup"
    }
}
```

#### Create a Snapshot

| Endpoint                                          | Method | Description       |
| ------------------------------------------------- | ------ | ----------------- |
| /\_snapshot/\<repository-name\>/\<snapshot-name\> | PUT    | Create a snapshot |

##### Request Body

```
{
    "expand_wildcards": "STRING",
    "ignore_unavailable": "BOOL",
    "include_global_state": "BOOL",
    "indices": "STRING || []",
    "feature_states": [],
    "metadata": {},
    "partial": "BOOL"
}
```

##### Example

```
PUT /_snapshot/example-repository/example-snapshot
{
    "indices": "example-index"
}
```

### Restore from a snapshot

| Endpoint                                                    | Method | Description             |
| ----------------------------------------------------------- | ------ | ----------------------- |
| /\_snapshot/\<repository-name\>/\<snapshot-name\>/\_restore | POST   | Restore from a snapshot |

##### Request Body

```
{
    "ignore_unavailable": "BOOL",
    "ignore_index_settings": "STRING || []",
    "include_aliases": "BOOL",
    "incldue_global_state": "BOOL",
    "feature_states": [],
    "index_settings": {},
    "indices": "STRING || []",
    "partial": "BOOL",
    "rename_pattern": "STRING",
    "rename_replacement": "STRING"
}
```

##### Example

```
POST /_snapshot/local-fs/example-snapshot/_restore
{
    "indices": "example-index"
}
```

## Configure a snapshot to be searchable

### Mount the snapshot for searching

| Endpoint                                                  | Method | Description                    |
| --------------------------------------------------------- | ------ | ------------------------------ |
| /\_snapshot/\<repository-name\>/\<snapshot-name\>/\_mount | POST   | Mount a snapshot for searching |

##### Request Body

```
{
    "index": "STRING",
    "renamed_index": "STRING",
    "index_settings": {},
    "ignore_index_settings": []
}
```

##### Example

```
POST /_snapshot/local-fs/example-snapshot/_mount
{
    "index": "example-index",
    "renamed_index": "example"
}
```

### Search the mounted snapshot

```
GET /example/_search
```

## Configure a cluster for cross-cluster search

Two Models

- API Based Authentication
- Certificate Authentication

Two Connection Methods

- Sniffing
- Proxy

### Update Cluster Settings

| Endpoint            | Method | Description             |
| ------------------- | ------ | ----------------------- |
| /\_cluster/settings | PUT    | Update cluster settings |

#### Example

```
PUT _cluster/settings
{
    "persistent": {
        "cluster": {
            "remote": {
                "remote-cluster-name":{
                    "mode": "sniff",
                    "seeds": [
                        "example.com:9500",
                        "127.0.0.1:9300"
                    ]
                }
            }
        }
    }
}
```

### Search Remote Cluster

#### Example

```
GET remote-cluster-name:example-index/_search
```

- https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-cross-cluster-search.html
- https://www.elastic.co/guide/en/elasticsearch/reference/current/remote-clusters-settings.html

## Implement cross-cluster replication

### Update Cluster Settings

| Endpoint            | Method | Description             |
| ------------------- | ------ | ----------------------- |
| /\_cluster/settings | PUT    | Update cluster settings |

#### Example

```
PUT _cluster/settings
{
    "persistent": {
        "cluster": {
            "remote": {
                "remote-cluster-name":{
                    "mode": "sniff",
                    "seeds": [
                        "example.com:9500",
                        "127.0.0.1:9300"
                    ]
                }
            }
        }
    }
}
```

### Create Cross-Cluster Replication Follower

| Endpoint                              | Method | Description             |
| ------------------------------------- | ------ | ----------------------- |
| /\<follower-index-name\>/\_ccr/follow | PUT    | Create a follower index |

#### CCR Follow Request Body

```
{
    "leader_index": "STRING",
    "remote_cluster": "STRING",
    "data_stream_name": "STRING",
    "settings": {},
    "max_read_request_operations_count": "INTEGER",
    "max_outstanding_read_requests": "LONG",
    "max_read_request_size": "BYTE_VALUE"
    "max_write_request_operation_count": "INTEGER",
    "max_write_request_size": "BYTE_VALUE",
    "max_outstanding_write_requests": "INTEGER",
    "max_write_buffer_count": "INTEGER",
    "max_write_buffer_size": "BYTE_VALUE"
    "max_retry_delay": "TIME_VALUE",
    "read_poll_timeout": "TIME_VALUE"
}
```

#### Example

```
PUT /example-index-copy/_ccr/follow
{
    "remote_cluster": "cluster01",
    "leader_index": "example-index"
}
```

- https://www.elastic.co/guide/en/elasticsearch/reference/current/ccr-put-follow.html
