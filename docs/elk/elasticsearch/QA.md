**QA**

Q. ignoring dangled index
```
ignoring dangled index [[.security/oI9Go4qPSlWw0ecwHxkmpw]] on node [{ops-prod-elastic10-al} due to an existing alias with the same name
```

A. close question
```yaml
1.登录到对应的实例上，删除对应的 indices. 
rm -f -r $DATA_PATH/nodes/0/indices/oI9Go4qPSlWw0ecwHxkmpw
```
