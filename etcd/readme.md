- 1. 启动etcd(单服务)

```
docker run -d -v /usr/share/ca-certificates/:/etc/ssl/certs -p 4001:4001 -p 2380:2380 -p 2379:2379 \
 --name etcd etcd /usr/local/bin/etcd \
 -name etcd0 \
 -advertise-client-urls http://192.168.3.3:2379,http://192.168.3.3:4001 \
 -listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
 -initial-advertise-peer-urls http://192.168.3.3:2380 \
 -listen-peer-urls http://0.0.0.0:2380 \
 -initial-cluster-token etcd-cluster-1 \
 -initial-cluster etcd0=http://192.168.3.3:2380 \
 -initial-cluster-state new

 ```


- 2. ETCD参数说明

```
data-dir:指定节点的数据存储目录，这些数据包括节点ID，集群ID，集群初始化配置，Snapshot文件，若未指定—wal-dir，还会存储WAL文件；
wal-dir:指定节点的was文件的存储目录，若指定了该参数，wal文件会和其他数据文件分开存储。
name: 节点名称
initial-advertise-peer-urls: 告知集群其他节点url.(对于集群内提供服务的url)
listen-peer-urls: 监听URL，用于与其他节点通讯
advertise-client-urls: 告知客户端url, 也就是服务的url(对外提供服务的utl)
initial-cluster-token: 集群的ID
initial-cluster: 集群中所有节点
```

