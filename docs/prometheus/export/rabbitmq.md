- url https://github.com/kbudde/rabbitmq_exporter
```yaml
[Unit]
Description=monitor rabbitmq plugins 
After=network-online.target
Before=shutdown.target

[Service]
Environment=RABBIT_URL=http://10.10.0.0:15672
Environment=RABBIT_USER=guest
Environment=RABBIT_PASSWORD=guest
Environment=PUBLISH_PORT=19419
Type=simple
User=root
Group=root
LimitNOFILE=65535
ExecStart=/data/app/rabbitmq_exporter/rabbitmq_exporter

Restart=always
StandardOutput=journal
```
