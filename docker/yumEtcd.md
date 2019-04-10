# 四.etcd服务的安装与配置

 

[竹莲心龙](https://www.jianshu.com/u/96574f6527aa) 

2016.10.26 15:30* 字数 52  阅读 12927评论  0喜欢 5

4.1.安装etcd

```
yum install etcd
```

4.2.准备目录

```
mkdir -p /work/etcd
chown etcd:etcd /work/etcd
```

4.3.备份

```
cp /etc/etcd/etcd.conf /etc/etcd/etcd.conf.template
```

4.4.参数说明

```
name      节点名称
data-dir      指定节点的数据存储目录
listen-peer-urls      监听URL，用于与其他节点通讯
listen-client-urls    对外提供服务的地址：比如 http://ip:2379,http://127.0.0.1:2379 ，客户端会连接到这里和 etcd 交互
initial-advertise-peer-urls   该节点同伴监听地址，这个值会告诉集群中其他节点
initial-cluster   集群中所有节点的信息，格式为 node1=http://ip1:2380,node2=http://ip2:2380,… 。注意：这里的 node1 是节点的 --name 指定的名字；后面的 ip1:2380 是 --initial-advertise-peer-urls 指定的值
initial-cluster-state     新建集群的时候，这个值为 new ；假如已经存在的集群，这个值为 existing
initial-cluster-token     创建集群的 token，这个值每个集群保持唯一。这样的话，如果你要重新创建集群，即使配置和之前一样，也会再次生成新的集群和节点 uuid；否则会导致多个集群之间的冲突，造成未知的错误
advertise-client-urls     对外公告的该节点客户端监听地址，这个值会告诉集群中其他节点
```

4.5.编辑配置文件

```
vim /etc/etcd/etcd.conf
# [member]
ETCD_NAME="k8s_master_ip_name"   #范例：etcd1
ETCD_DATA_DIR="/work/etcd"
ETCD_LISTEN_PEER_URLS="http://k8s_master_ip:2380"
ETCD_LISTEN_CLIENT_URLS="http://127.0.0.1:2379,http://k8s_master_ip:2379"

#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://k8s_master_ip:2380"
ETCD_INITIAL_CLUSTER="k8s_master_ip_name=http://k8s_master_ip:2380"
ETCD_INITIAL_CLUSTER_STATE="new"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_ADVERTISE_CLIENT_URLS="http://k8s_master_ip:2379"

#如果k8s-master是集群，配置不同之处如下：
ETCD_INITIAL_CLUSTER="etcd1=http://k8s_master_ip1,etcd2=http://k8s_master_ip2:2380"
```

4.6.编辑启动脚本

```
vim /usr/lib/systemd/system/etcd.service
ExecStart=/bin/bash -c "GOMAXPROCS=$(nproc) /usr/bin/etcd --name=\"${ETCD_NAME}\" --data-dir=\"${ETCD_DATA_DIR}\" --listen-client-urls=\"${ETCD_LISTEN_CLIENT_URLS}\" --listen-peer-urls=\"${ETCD_LISTEN_PEER_URLS}\" --advertise-client-urls=\"${ETCD_ADVERTISE_CLIENT_URLS}\" --initial-advertise-peer-urls=\"${ETCD_INITIAL_ADVERTISE_PEER_URLS}\" --initial-cluster=\"${ETCD_INITIAL_CLUSTER}\" --initial-cluster-state=\"${ETCD_INITIAL_CLUSTER_STATE}\""
```

4.7.启动etcd服务

```
systemctl daemon-reload
systemctl enable etcd.service
systemctl start etcd.service
```

4.8.创建网络信息

```
etcdctl mkdir /k8s/network
etcdctl set /k8s/network/config '{"Network":"172.100.0.0/16"}'
```

 