***install*** 

- build project
```
# clone local
git clone git@github.com:apache/rocketmq-exporter.git
cd rocketmq-exporter
# update configuartion
vim /rocketmq-exporter/src/main/resources/application.properties 
server.port=15557

spring.application.name=rocketmq-exporter
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
spring.http.encoding.force=true
logging.config=classpath:logback.xml
#if this value is empty,use env value rocketmq.config.namesrvAddr  NAMESRV_ADDR
rocketmq.config.namesrvAddr=mq01:9876


rocketmq.config.enableCollect=true
rocketmq.config.webTelemetryPath=/metrics
rocketmq.config.rocketmqVersion=4_3_2
# mvn install 
mvn -f pom.xml clean install
```

- systemctl service
```yaml
[Unit]
Description=prometheus https://github.com/apache/rocketmq-exporter 
After=syslog.target network.target

[Service]
Environment=OPTS="" PROFILE=""
WorkingDirectory=/data/app/rocketmq-exporter-0.0.1
ExecStart=/usr/java/default/jre/bin/java $OPTS -jar rocketmq-exporter-0.0.1-SNAPSHOT.jar $PROFILE
SuccessExitStatus=0 143
PrivateTmp=false
LimitNOFILE=1000000
LimitNPROC=100000
TimeoutStopSec=10s
RestartSec=60s
Restart=on-failure
User=root
Group=root

[Install]
WantedBy=multi-user.target
```
