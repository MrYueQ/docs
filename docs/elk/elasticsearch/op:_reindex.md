***_reindex***

---
- reindex remote

**source remote witelist enable**

```yaml
POST _reindex
{
  "source": {
    "remote": {
      "host": "http://otherhost:9200",
      "connect_timeout": "10s"
    },
    "index": "source",
    "size": 10000
    "query": {
      "match_all": {}
    }
  },
  "dest": {
    "index": "dest"
  }
}
```
---

- get index mapping
```yaml
# act: Format curl esConn:httpPort/IndexName
curl es01-data-demo:19200/indexName?pretty=true
```

- put new index
```yaml
# act: 
curl -XPUT 'http://es01-data-demo:19200/NewIndexName'  \
  -H 'Content-Type: application/json' \
-d '
{
        "mappings":{
                    "version":{
                        "type":"integer"
                    },
                    "yn":{
                        "type":"integer"
                    }
                }
            }
        },
        "settings":{
            "index":{
                "number_of_shards":"5",
                "number_of_replicas":"0"
            }
        }
    }
    '
    
# check new index 
curl -XGET http://es01-data-demo:19200/NewIndexName
```

- create pipeline
```yaml
# create pipeline
curl -XPUT http://es01-data-demo:19200/_ingest/pipeline/index_remove_fields -d '
{
  "description": "Removes sales_order excelMonth field", 
  "processors": [
    {
      "remove": {
        "field": "excelMonth"
      }
    }
  ]
}
'

# get pipeline status
curl -XGET http://es01-data-demo:19200/_ingest/pipeline/index_remove_fields?pretty=true
# respon result
{
  "index_remove_fields" : {
    "description" : "Removes sales_order excelMonth field",
    "processors" : [
      {
        "remove" : {
          "field" : "excelMonth"
        }
      }
    ]
  }
}

```

- create _reindex task
```yaml
curl -XPOST http://es01-data-demo:19200/_reindex?wait_for_completion=false -d '
{
  "conflicts": "proceed",
  "source": {
    "index": "indexName",
     "size": 10000
  },
  "dest": {
    "index": "NewIndexName",
    "pipeline": "index_remove_fields",
    "op_type": "create"
  }
}
'

# check task status
curl -XGET http://es01-data-demo:19200/_tasks?detailed=true&actions=*reindex
```

----
***example***

```yaml
POST _reindex?wait_for_completion=false
{
  "conflicts": "proceed",
  "source": {
    "index": "reindex-indexName-stats",
     "size": 10000,
     "query":{
        "bool":{
            "must":[
                {
                    "match_phrase":{
                        "domain":{
                            "query":"www.baidu.com"
                        }
                    }
                },
                {
                    "exists":{
                        "field":"geoip.ip"
                    }
                }
            ]
          }
      }
  },
  "dest": {
    "index": "new-indexName-timestamp",
    "op_type": "create"
  }
}
```
----

- logstash configuration
```yaml
input {
  # Read all documents from Elasticsearch matching the given query
  elasticsearch {
    hosts => [ "esConn"]
    index => "IndexName"
    query => '{
  "query": {
    "match_all": {}
  }
}'
    size => 10000
    docinfo => true
    scroll => "3m"
}
}

filter  {
  json {
    source => "message"
    }
    if[excelMonth]{
    mutate{
        remove_field => [ "excelMonth" ]
        }
    }
}

output {
   elasticsearch {
    hosts => [ "esConn"]
    document_type => "%{[@metadata][_type]}"
    document_id => "%{[@metadata][_id]}"
    index => "NewIndexName"
    http_compression => true
   }
}

```

----
- alias
```yaml
curl -XPOST esConn/_aliases -d '
{
    "actions" : [
        { "add" : { "index" : "NewIndexName", "alias" : "AliasName" } },
        { "remove" : { "index" : "OldIndexName", "alias" : "AliasName" } }
    ]
}
'
```
