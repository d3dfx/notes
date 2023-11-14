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
## Define and use a custom analyzer that satisfies a given set of requirements

## Define and use multi-fields with different data types and/or analyzers

## Use the Reindex API and Update By Query API to reindex and/or update documents

## Define and use an ingest pipeline that satisfies a given set of requirements, including the use of Painless to modify documents

## Define runtime fields to retrieve custom values using Painless scripting

