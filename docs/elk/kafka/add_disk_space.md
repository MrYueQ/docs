***Disk Append Space***

----
- create data path 
```shell
mkdir -p /data1/kafka/data
chown -R kafka:kafka /data1/kafka/data
```

- update kafka server.configuration
```shell
log.dirs=/data/kafka/data,/data1/kafka/data
```

- stop kafka server
```shell 
# 
cd ${KAFKA_BIN}
./kafka-server-stop.sh 

# check server status 
jps -ml

```

- move data 
```shell
mv topicName NewDataPath
```

- start kafka server 
```shell
./kafka-server-start.sh -daemon ../config/server.properties

# check server status 
jps -ml 

# check topic group status 
/kafka-consumer-groups.sh --bootstrap-server kafkaConn --group KafkaGroup  --describe
```
