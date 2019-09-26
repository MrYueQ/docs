***configuration LDAP***

----
- ldap configuartion example
```yaml
[[servers]]
host = "dc01Con,dc02Con"
port = 389
use_ssl = false
start_tls = false
ssl_skip_verify = false
bind_dn = "CN=auth,CN=Users,DC=corp,DC=demo,DC=com"
bind_password = 'password'
search_filter = "(sAMAccountName=%s)"
search_base_dns = ["OU=XYY,DC=corp,DC=demo,DC=com"]
[servers.attributes]
name = "givenName"
surname = "sn"
username = "sAMAccountName"
member_of = "memberOf"
email =  "email"
# # Map ldap groups to grafana org roles
[[servers.group_mappings]]
group_dn = "CN=系统运维组,OU=XYY_GROUP,DC=corp,DC=demo,DC=com"
org_role = "Viewer"
org_id = 1
[[servers.group_mappings]]
group_dn = "CN=ops.ec.crm,OU=XYY_GROUP,DC=corp,DC=demo,DC=com"
org_role = "Viewer"
org_id = 2

```
