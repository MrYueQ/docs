***docker install***

----
- Use the host IP address when configuring etcd:
```shell
export NODE1=192.168.1.8
```

- Configure a Docker volume to store etcd data:

```shell
docker volume create --name etcd-data
export DATA_DIR="etcd-data"
```
- Run the latest version of etcd:

```shell
REGISTRY=quay.io/coreos/etcd
# available from v3.2.5
REGISTRY=gcr.io/etcd-development/etcd
```
```yaml
docker run \
  -p 2379:2379 \
  -p 2380:2380 \
  --volume=${DATA_DIR}:/etcd-data \
  --name etcd ${REGISTRY}:latest \
  /usr/local/bin/etcd \
  --data-dir=/etcd-data --name node1 \
  --initial-advertise-peer-urls http://${NODE1}:2380 --listen-peer-urls http://0.0.0.0:2380 \
  --advertise-client-urls http://${NODE1}:2379 --listen-client-urls http://0.0.0.0:2379 \
  --initial-cluster node1=http://${NODE1}:2380
```
- List the cluster member:
```shell
etcdctl --endpoints=http://${NODE1}:2379 member list
```
### https://github.com/etcd-io/etcd/blob/master/Documentation/op-guide/container.md
