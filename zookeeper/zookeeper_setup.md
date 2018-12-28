# 准备工作：（zookeeper放在目录/zookeeper下）
```
mkdir zookeeper
cd zookeeper
```

本例子采用伪集群模式就是在同一主机启动多个zookeeper并组成集群，下边以在110.39.13.77主机上创3个zookeeper组集群为例

将前边的zookeeper复制成zookeeper1/zookeeper2/zookeeper3三份，然后修改conf/zoo.cfg文件，以zookeeper1为例如下：

## 1. 下载（3.4.11版本）
```
wget <http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-3.4.12/zookeeper-3.4.12.tar.gz>
```
## 2. 解压
```
tar -zxf zookeeper-3.4.11.tar.gz
```
## 3. 修改config文件

在/zookeeper/zookeeper-3.4.11/conf/下有一个文件zoo_sample.cfg，把它改成zoo.cfg

## 4. 在/zookeeper/zookeeper-3.4.11下创建两个目录data、log，用来放数据和日志

​   /zookeeper/zookeeper-3.4.11/data

​    /zookeeper/zookeeper-3.4.11/log
## 5.把zookeeper-3.4.11copy3份，分别为zk1，zk2，zk3，我们就用这3个做集群
## 6.分别修改3个zk的conf/zoo.cfg文件如下：
```
tickTime=2000
dataDir=/zookeeper/zk1/data
dataLogDir=/zookeeper/zk1/logs
clientPort=2181
initLimit=5
syncLimit=2
server.1=110.39.13.77:2888:3888
server.2=110.39.13.77:4888:5888
server.3=110.39.13.77:6888:7888
\-----------------------------------------------------------------
tickTime=2000
dataDir=/zookeeper/zk2/data
dataLogDir=/zookeeper/zk2/logs
clientPort=2182
initLimit=5
syncLimit=2
server.1=110.39.13.77:2888:3888
server.2=110.39.13.77:4888:5888
server.3=110.39.13.77:6888:7888
\-----------------------------------------------------------------------
tickTime=2000
dataDir=/zookeeper/zk3/data
dataLogDir=/zookeeper/zk3/logs
clientPort=2183
initLimit=5
syncLimit=2
server.1=110.39.13.77:2888:3888
server.2=110.39.13.77:4888:5888
server.3=110.39.13.77:6888:7888
```
## 7. 然后需要在data目录下创建myid文件，写入自己是几号server，以zookeeper1（server.1）为例：
```
echo '1' > /zookeeper/zk1/data/myid
```
## 9. 启动zk集群
```
/zookeeper/zk1/bin/zkServer.sh start
/zookeeper/zk2/bin/zkServer.sh start
/zookeeper/zk3/bin/zkServer.sh start
```

备注：（zk的一般命令）
```
./zkServer.sh start ./zkServer.sh stop ./zkServer.sh restart
./zkServer.sh status
```