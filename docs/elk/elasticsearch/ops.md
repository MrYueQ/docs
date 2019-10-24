***ops***

- enable/disable index slow log

```yaml
curl -X PUT \
  http://ip:port/IndexName/_settings \
  -H 'Content-Type: application/json' \
  -H 'cache-control: no-cache' \
  -d '{
     "index.search.slowlog.threshold.query.warn": "10s",
    "index.search.slowlog.threshold.query.info": "5s",
    "index.search.slowlog.threshold.query.debug": "2s",
    "index.search.slowlog.threshold.query.trace": "500ms",
    "index.search.slowlog.threshold.fetch.warn": "1s",
    "index.search.slowlog.threshold.fetch.info": "800ms",
    "index.search.slowlog.threshold.fetch.debug": "500ms",
    "index.search.slowlog.threshold.fetch.trace": "200ms",
    "index.search.slowlog.level": "debug",
    "index.indexing.slowlog.threshold.index.warn": "10s",
    "index.indexing.slowlog.threshold.index.info": "5s",
    "index.indexing.slowlog.threshold.index.debug": "2s",
    "index.indexing.slowlog.threshold.index.trace": "500ms",
    "index.indexing.slowlog.level": "trace",
    "index.indexing.slowlog.source": "1000"
}'
```

- elastic service logstash filter

```yaml
filter {
    ####
    # json format 
    json {
        source => "message"
        }
    grok {
        match => { "message" => [ "(?m)^\[(?<logTime>[^\]]+)\]\[(?<loglevel>[^ ]+)\s\]\[(?<class>[^\]]+)\]\s\[(?<nodeName>[^\]]+)\]\s\[(?<indexName>[^\]]+)\]" ]        }
    }
   mutate {    
      remove_field => [ "source" ]
   }   
    date {
        match => [ "logTime", "yyyy-MM-dd'T'HH:mm:ss,SSS" ]
        target => "logTime"
    }
}
```

- elastic service slow log filter

```yaml
filter { 
    grok {
        match => { "message" => [ "(?m)^\[(?<logTime>[^\]]+|)\]\[(?<level>[^\]]+|)\]\[(?<className>[^\]]+|)\] \[(?<nodeName>[^\]]+|)\] \[(?<indexName>[^\]]+|)\]\[(?<shardNumber>[^\]]+|)\] took\[(?<took>[^\]]+|)\],\stook_millis\[(?<tookMillis>[^\]]+|)\],\stypes\[(?<types>[^\]]+|)\],\sstats\[(?:<stats>[^\]]+|)\],\ssearch_type\[(?<searchType>[^\]]+|)\],\stotal_shards\[(?<totalShards>[^\]]+|)\],\ssource\[(?<dsl>.*)\]," ]
        }
    }
   if ![tags] {
     mutate {    
      remove_field => [ "message" ]
    }
   }
   mutate {    
      remove_field => [ "source" ]
   }   
    date {
        match => [ "logTime", "yyyy-MM-dd'T'HH:mm:ss,SSS" ]
        target => "logTime"
    }
}

```

- elastic: if else struct

```yaml
filter {
  json {
      source => "message"
      }
  json {
      source => "message"
      }
  grok {
    match => {
        "source" => [ 
            "^/data/elasticsearch/data04/rds_slow_log/rds-slow-log-rr-(?<instance>[^-]+)",
            "^/data/elasticsearch/data04/rds_slow_log/rds-slow-log-rm-(?<instance>[^-]+)"
            ]
        }
    }
  if [instance] == "A" {
       mutate {
        update => { "instance" => "prd-master" }
      }
  } else if [instance] == "B" {
       mutate {
        update => { "instance" => "prd-slave1" }
      }  
  } else if [instance] == "C" {
       mutate {
        update => { "instance" => "prd-slave2" }
      }  
  }  
  mutate {
        remove_field => [ "message","source" ]
     }
  date {
    match => [ "CreateTime", "yyyy-MM-dd'Z'","UNIX_MS" ]
    target => "CreateTime"
    }
```

- enable elastic gc [low version] 
```shell
vim jvm.options
# update loggc
-Xloggc:SystemFilePath/FileName
```

- new index mapping

```yaml
PUT ops.projectName.ServiceName
{
    "mappings" : {
      "doc" : {
        "properties" : {
          "datetime" : {
            "type" : "date"
          },
          "domain" : {
            "type" : "keyword"
          },
          "tags" : {
            "type" : "keyword"
          },
          "ua" : {
            "properties" : {
              "build" : {
                "type" : "keyword"
              },
              "device" : {
                "type" : "keyword"
              },
              "major" : {
                "type" : "keyword"
              },
              "minor" : {
                "type" : "keyword"
              },
              "name" : {
                "type" : "keyword"
              },
              "os" : {
                "type" : "keyword"
              },
              "os_major" : {
                "type" : "keyword"
              },
              "os_minor" : {
                "type" : "keyword"
              },
              "os_name" : {
                "type" : "keyword"
              },
              "patch" : {
                "type" : "keyword"
              }
            }
          },
          "user_agent" : {
            "type" : "keyword"
          },
          "x_forwarded_for" : {
            "type" : "keyword"
            }
          }
        }
      }
    }
```

----

***shard***

- reroute
```yaml
POST /_cluster/reroute
{
    "commands" : [
        {
            "move" : {
                "index" : "test", "shard" : 0,
                "from_node" : "node1", "to_node" : "node2"
            }
        },
        {
          "allocate_replica" : {
                "index" : "test", "shard" : 1,
                "node" : "node3"
          }
        }
    ]
}

https://www.elastic.co/guide/en/elasticsearch/reference/6.2/allocation-filtering.html

curl -XPUT  '_cluster/settings' -d '{
  "transient" : {
    "cluster.routing.allocation.exclude._name" : "es02-A-hd1"
  }
}
'

index.routing.allocation.primaries

https://www.elastic.co/guide/en/elasticsearch/reference/5.2/shards-allocation.html
```

----

- docs crud

```yaml
# update field value
POST demo/doc/1/_update
{
    "doc" : {
        "geoip.ip" : "172.16.1.1"
    },
    "detect_noop": false
}

# update netst type fields value
POST demo/doc/1/_update
{
    "script" : "ctx._source.geoip.ip = '172.16.1.1'"
}

```
