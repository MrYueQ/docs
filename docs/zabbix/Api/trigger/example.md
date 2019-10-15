***exampel***

----

- search host
```yaml
# org url
# https://www.zabbix.com/documentation/4.2/manual/api/reference/trigger/get
# 过滤 主机名称为 demo01 中 触发器
{
    "jsonrpc": "2.0",
    "method": "trigger.get",
    "params": {
    	"selectFunctions": "extend",
    	"output": [
            "triggerid",
            "description",
            "priority",
            "expression"
        ],
    	"filter": {
    	        "host":"demo01",
              "status":1
    	}
    },
    "auth": "5c17b2d9061803f8adecec74f62beb60",
    "id": 1
}
```
- host.get
```yaml
{
    "jsonrpc": "2.0",
    "method": "host.get",
    "params": {
    	"selectItems":["itemid", "key_", "delay"],
    		"filter": {
    			"host":"cmdb02-ops-prod-bj1"
    		},
   "selectTriggers": ["triggerid", "templateid", "state"],
   "output": "hostid"
    },
    "auth": "5c17b2d9061803f8adecec74f62beb60",
    "id": 1
}
```

- item.get
```yaml
{
    "jsonrpc": "2.0",
    "method": "item.get",
    "params": {
    	"selectTriggers":"extend",
    		"filter": {
    			"host":"cmdb02-ops-prod-bj1",
    			"key_":"vm.memory.size[available]"
    		}
    },
    "auth": "5c17b2d9061803f8adecec74f62beb60",
    "id": 1
}
```
