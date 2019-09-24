***kafka topic reassign***

---

- reassign topic 
```yaml
# cat > topics-to-move.json  <<"EOF"
{"topics": [
{"topic": "ops-ref-prod"}
],
"version":1
}
EOF
```

- describe topic stats

```shell 
./kafka-topics.sh --describe --zookeeper zkConn --topic ops-ref-prod
```

- generate

```shell
./kafka-reassign-partitions.sh  --zookeeper zkConn --topics-to-move-json-file /home/kafka/scroll/topics-to-move.json --broker-list "4,5" --generate
```

- execute

```shell
./kafka-reassign-partitions.sh  --zookeeper zkConn --reassignment-json-file /home/kafka/scroll/sugg.json  --execute
```

- verify

```shell 
./kafka-reassign-partitions.sh  --zookeeper zkConn --reassignment-json-file /home/kafka/scroll/sugg.json  --verify
```
