----
***Zabbix Data Source***

- enable zabbix data source
```shell
Configuartion --> Plugins --> zabbix --> Enable
```
- config data source is zabbix
```shell
Configuartion --> Data Sources --> zabbix 


# Zabbix API details
# 这部分目前借助zabbix 内部提供的隔离机制
*/
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

***Add LDAP GROUP***

```yaml
# add ldap.toml 
[[servers.group_mappings]]
group_dn = "CN=groupName,OU=XYY_GROUP,DC=corp,DC=domain,DC=com"
# group grant
org_role = "Editor"
# org auto 
org_id = 3
```
