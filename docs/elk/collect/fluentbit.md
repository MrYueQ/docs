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
