# 1. 配置容器选择andyshinn/dnsmasq的docker镜像，2.75版本。执行命令
```
docker run -d -p 53:53/tcp -p 53:53/udp -v /var/lib/dns/etc:/etc --cap-add=NET_ADMIN --name dns-server andyshinn/dnsmasq:2.75
```
本以为顺利完成，结果报错：docker: Error response from daemon: failed to create endpoint dns-server on network bridge: Error starting userland proxy: listen tcp 0.0.0.0:53: bind: address already in use.dns服务默认是用的53端口被占用了。查看本机端口占用情况：
```
netstat -lnp|grep 53
```
发现在宿主机器上有一个dnsmasq服务。google一番了解到，原来是Ubuntu默认安装了dnsmasq-base服务。官网提到：Note that the package “dnsmasq” interferes with Network Manager which can use “dnsmasq-base” to provide DHCP services when sharing an internet connection. Therefore, if you use network manager (fine in simple set-ups only), then install dnsmasq-base, but not dnsmasq. If you have a more complicated set-up, uninstall network manager, use dnsmasq, or similar software (bind9, dhcpd, etc), and configure things by hand.通过kill 停掉该服务。再次执行上述命令，通过
```
docker ps
```
查看，容器启动成功。
# 2. 配置DNS服务进入容器
```docker exec -it dns-server /bin/sh```
首先配置上行的真正的dns服务器地址，毕竟你只是个本地代理，不了解外部规则。创建文件：
```vi /etc/resolv.dnsmasq```
添加内容：

```
nameserver 114.114.114.114
nameserver 8.8.8.8
```
配置本地解析规则，这才是我们的真实目的。新建配置文件
​```vi /etc/dnsmasqhosts```
添加解析规则
```
10.22.29.201 nginx.wuhanenn.cn
10.22.29.201 rancher1.wuhanenn.cn
10.22.29.202 rancher2.wuhanenn.cn
10.22.29.203 rancher3.wuhanenn.cn
10.22.29.201 rancher.wuhanenn.cn
10.22.29.202 rancher.wuhanenn.cn
10.22.29.203 rancher.wuhanenn.cn
```
 修改dnsmasq配置文件，指定使用上述两个我们自定义的配置文件
```vi /etc/dnsmasq.conf```
修改下述两个配置

```
resolv-file=/etc/resolv.dnsmasq
addn-hosts=/etc/dnsmasqhosts
```
回到宿主，重启dns-server容器服务。
```
docker restart dns-server
```