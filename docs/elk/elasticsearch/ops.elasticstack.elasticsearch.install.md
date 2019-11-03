**cluster install**

---
```
1. 升级操作期间，暂停ES集群数据写入操作
2. 建议把当前集群主节点留到最后一个实例来升级
```
----

**ready**

| 序号 | 组件名称 | 组件版本 | 备注 |
|:---:|:---:|:---:|:---:|
| 1 | elasticsearch | 6.8.2 | |

**rolling upgrades**

- ***routing alloction settings***
```yaml
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "primaries"
  }
}
```

- ***stop non-essential indexing synced flush***
```yaml
POST _flush/synced
```

- ***node restart***

1. copy config and certs
```
cd /data/app/elasticsearch-6.8.2/config
rm -f *
cp -r  /data/app/elasticsearch-6.5.4/config/* .
```

2. shutdown old node
```
kill node_processid
```

3. start new node
```
./bin/elasticsearch -d
```

- ***reenable shard allocation***

```yaml
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
```

- ***wait node recover***

```yaml
GET _cat/health?v
GET _cat/recovery
```

- ***repeat***
