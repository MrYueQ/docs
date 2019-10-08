----
- enable zabbix data source
Configuartion --> Plugins --> zabbix --> Enable

- config data source is zabbix

```yaml
# add zabbix mysql 
Configuartion --> Data Sources --> zabbix
```
----

***HTML Not working in Text panel***

```yaml
# question
HTML is not working in Text panel using Grafana 6

# issue 
https://github.com/grafana/grafana/issues/15647

# method
1. setting export
export GF_PANELS_DISABLE_SANITIZE_HTML=true

2. add configuartion [grafana.ini]
[panels]
disable_sanitize_html = true

```
