***install*** 

- update yum source
```shell
cat > /etc/yum.repos.d/td-agent-bit.repo <<EOF
[td-agent-bit]
name = TD Agent Bit
baseurl = http://packages.fluentbit.io/centos/7
gpgcheck=1
gpgkey=http://packages.fluentbit.io/fluentbit.key
enabled=1
EOF
```
- yum download
```shell
mkdir rpm_temp_storage
cd rpm_temp_storage
yum install --downloadonly --downloaddir=./ td-agent-bit
```
- scp repo server
```shell
rpm -ihv http://repo.com/centos/7/x86_64/td-agent-bit-1.3.2-1.x86_64.rpm
createrepo /data/www/centos/7/x86_64/
yum install td-agent-bit
```

- example configuartion

```yaml
[SERVICE]
    flush                     1
    log_Level                 info
    storage.path              /var/lib/fluentbit/
    storage.sync              normal
    storage.checksum          off
    storage.backlog.mem_limit 500M
    
[INPUT]
    name          tail
    storage.type  filesystem


```

- @INCLUDE somefile.conf
```shell
The configuration reader will try to open the path somefile.conf, if not found, it will assume it's a relative path based on the path of the base configuration file, e.g:

Main configuration file path: /tmp/main.conf
Included file: somefile.conf
Fluent Bit will try to open somefile.conf, if it fails it will try /tmp/somefile.conf.
```

- Retry_Limit
- Buffer_Max_Size 
- Tag

-----

- tools
```yaml
# add field hostname
cd  /opt/td-agent-bit/bin
./td-agent-bit -i mem -o stdout -F record_modifier -p 'Record=hostname ${HOSTNAME}' -m '*'

# add field hostname
./td-agent-bit -i mem -o stdout -F record_modifier -p 'Record=hostname ${ADDRESS}' -m '*'

```
- example configuartion

```conf
[SERVICE]
    Flush        5
    Daemon       off
    Log_Level    info
    Parsers_File /etc/td-agent-bit/parsers/parsers.conf
    Plugins_File /etc/td-agent-bit/plugins/plugins.conf
    storage.path              /var/lib/fluentbit/
    storage.sync              normal
    storage.checksum          off
    storage.backlog.mem_limit 500M
    
    HTTP_Server  On
    HTTP_Listen  0.0.0.0
    HTTP_Port    12888


[INPUT]
    Name tail
    Path logpath.*
    storage.type  filesystem
    Buffer_Max_Size 32M
    Exclude_Path *.lck
    Refresh_Interval 120
    Rotate_Wait 10
    Parser sentinel
    DB /var/lib/fluentbit/sentinel.db
	
[FILTER]
    Name record_modifier
    Match *
    Record hostname ${HOSTNAME}
	
[OUTPUT]  
    Name  kafka
    Match *
    Format json
    Brokers brokersList
    Topics saas-sentinel-stage
    Timestamp_Key queueTime
    Timestamp_Format iso8601
    rdkafka.request.required.acks 1
    rdkafka.log.connection.close false
```
 paraser
```yaml
[PARSER]
    Name   sentinel
    Format regex
    Regex (?<clock>^[^\|]+)\|(?<logTime>[^\|]+)\|(?<resourceName>[^\|]+)\|(?<passedQPS>[^\|]+)\|(?<blockedQPS>[^\|]+)\|(?<exitQPS>[^\|]+)\|(?<panicNumber>[^\|]+)\|(?<RT>[^\|]+)\|
    Time_Key clock
    Decode_Field_As escaped_utf8 logTime do_next
    Decode_Field_As escaped_utf8 resourceName do_next
```
