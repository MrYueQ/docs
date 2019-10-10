***install docker***

```shell
https://www.zabbix.com/documentation/current/manual/installation/containers
```
- Mysql Server instance
```yaml
docker run --name mysql-server -t \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -d mysql:5.7 \
      --character-set-server=utf8 --collation-server=utf8_bin
```

- Zabbix Java gateway
```yaml
docker run --name zabbix-java-gateway -t \
      -d zabbix/zabbix-java-gateway:latest
```

- Zabbix Server
```yaml
docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
      --link mysql-server:mysql \
      --link zabbix-java-gateway:zabbix-java-gateway \
      -p 10051:10051 \
      -d zabbix/zabbix-server-mysql:latest
```

- Web 
```yaml
docker run --name zabbix-web-nginx-mysql -t \
      -e DB_SERVER_HOST="mysql-server" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      --link mysql-server:mysql \
      --link zabbix-server-mysql:zabbix-server \
      -p 10080:80 \
      -d zabbix/zabbix-web-nginx-mysql:latest
```

- agent
```yaml
docker run --name zabbix-agent-v1.0 --link zabbix-server-mysql:zabbix-server-mysql -d zabbix/zabbix-agent:latest

docker exec -ti zabbix-agent-v1.0 /bin/bash
```
