\1. 增加docker加速镜像
curl -sSL <https://get.daocloud.io/daotools/set_mirror.sh>  | sh -s <http://1b1e621a.m.daocloud.io>

\2. docker run增加到host文件

--add-host puppetmaster:192.168.0.3

\3. 安装mysql

docker run --name mysql -e MYSQL_ROOT_PASSWORD=1QAZxsw@ -d -v  /var/log/enn/mysql/logs:/logs -v /var/lib/mysql:/var/lib/mysql -p 3306:3306  docker.io/mysql:5.7 --character-set-server=utf8mb4  --collation-server=utf8mb4_unicode_ci

4.安装jenkins

docker run --name jenkins -p 8080:8080 -p 50000:50000 -u 0 -v  /var/jenkins_home:/var/jenkins_home --add-host source.enncloud.cn:10.39.15.238  --add-host maven.enncloud.cn:10.39.25.48 --add-host paas.enncloud.cn:10.39.1.234  --add-host api.enncloud.cn:10.38.240.7 --add-host  tds02.xinaogroup.com:10.37.32.17 --add-host  edc2ml14a.xinaogroup.com:10.37.33.180 --add-host config-center-dev:10.39.45.2  --add-host config-center-fat:10.39.45.2 --add-host  console.enncloud.cn:10.38.240.24 jenkins

 

\5. docker卸载
列出你安装过的包

$ yum list installed | grep docker
docker-engine.x86_64   
1.7.1-1.el7  @/docker-engine-1.7.1-1.el7.x86_64.rpm

删除安装包

$ sudo yum -y remove docker.x86_64 docker-client.x86_64  docker-common.x86_64

删除镜像/容器等

$ rm -rf /var/lib/docker

\6. docker重启次数

docker update --restart=on-failure:3 dockerName
docker update  --restart=always dockerName


 