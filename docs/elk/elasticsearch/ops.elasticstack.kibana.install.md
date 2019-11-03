
**ready**

| 序号 | 组件名称 | 组件版本 | 备注 |
|:---:|:---:|:---:|:---:|
| 1 | kibana | 6.8.2 | |


- **install**

1. copy certs 
```
cd /data/app/kibana-6.8.2/config/
mkdir certs
cp -r /data/app/kibana-6.5.4/config/certs/* ./certs
```
2. update config
```
elasticsearch.hosts:
server.ssl.enabled: true
server.ssl.certificate: 
server.ssl.key: 
elasticsearch.ssl.certificate: 
elasticsearch.ssl.key: 
elasticsearch.ssl.certificateAuthorities: 
```
