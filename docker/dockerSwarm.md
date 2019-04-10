https://blog.csdn.net/candcplusplus/article/details/53836703
创建管理端
docker swarm init --advertise-addr 192.168.59.128

[root@i-vdehn6wt ~]# docker swarm init --advertise-addr 10.39.48.169
Swarm initialized: current node (05l9ilwtzyw8ywhmkyf9ce9vd) is now a manager.

To add a worker to this swarm, run the following command:    
	docker swarm join \
    --token SWMTKN-1-08hktg7motccrzucgubmtf5ldxnzwuy9nxuyaae8zrocnc1mw4-8aeoq6361u1i7kel179bez1m1 \
    10.39.48.169:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

查询token
docker swarm join-token worker
docker swarm join-token manager 

创建服务
docker service create --replicas 1 --name helloworld busybox:1.25.1-musl /bin/sh -c "while true; do echo hello world; sleep 3; 
done" 04a3iqg8zlhba84kpi2tatssf 

查看服务列表
docker service ls

查看服务详情
docker service inspect --pretty helloworld

查看在哪个节点在运行该服务
docker service ps helloworld

伸缩服务
docker service scale helloworld=5

删除swarm上运行的服务
docker service rm helloworld 

创建一个新的 overlay 网络
docker network create --driver overlay swarm_test

部署的服务使用 swarm_test 跨主机网络
docker service create --replicas 2 --name helloworld --network=swarm_test nginx:alpine