# docker命令集合

## 基础命令
systemctl start docker // 启动docker
sudo systemctl daemon-reload // 守护进程重启
systemctl restart  docker // 重启docker服务
sudo service docker restart // 重启docker服务
service docker stop // 关闭docker
systemctl stop docker // 关闭docker
docker rmi [镜像名字] // 删除docker镜像
docker image rm // 删除docker镜像
docker ps // 查询正在运行的container容器
dokcer ps -a // 查询所有container容器
docker stop containerId/Name // 暂停某个容器
coker rm contanerId/Name // 删除某个容器
docker stop $(docker ps -a -q) // 停止所有容器 
docker rm $(docker ps -a -q) // 删除所有容器
docker container restart egmp-console-test // 重启某个容器
docker exec -it [容器ID] /bin/bash // 进入某个container容器
exit // 退出container容器

## nginx容器命令
1,以下命令使用 NGINX 默认的配置来启动一个 Nginx 容器实例：
docker run --name runoob-nginx-test -p 8081:80 -d nginx
2,首先，创建目录 nginx, 用于存放后面的相关东西。
mkdir -p /sourceNginx/www /sourceNginx/logs /sourceNginx/conf
3,拷贝容器内 Nginx 配置文件到本地当前目录下的 sourceNginx 目录：
docker cp runoob-nginx-test:/etc/nginx/nginx.conf /sourceNginx/conf
说明：
 www: 目录将映射为 nginx 容器配置的虚拟目录。
 logs: 目录将映射为 nginx 容器的日志目录。
 conf: 目录里的配置文件将映射为 nginx 容器的配置文件。
提醒：如果想把/etc/nginx/配置文件全部挂载出来，需要注意新建启动容器时的挂载目录层次要对上，比如如下命令，会在/sourceNginx下新增nginx文件夹，实现nginx配置目录全部挂载
docker cp runoob-nginx-test:/etc/nginx /sourceNginx
4,部署命令
docker run -d -p 90:80 --name source-nginx -v /sourceNginx/www:/usr/share/nginx/html -v /sourceNginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /sourceNginx/logs:/var/log/nginx nginx
说明：
-p 90:80： 将容器的 80 端口映射到主机的 90 端口。
--name source-nginx：将容器命名为 source-nginx。
-v /sourceNginx/www:/usr/share/nginx/html：将我们自己创建的 www 目录挂载到容器的 /usr/share/nginx/html。
-v /sourceNginx/conf/nginx.conf:/etc/nginx/nginx.conf：将我们自己创建的 nginx.conf 挂载到容器的 /etc/nginx/nginx.conf。
-v /sourceNginx/logs:/var/log/nginx：将我们自己创建的 logs 挂载到容器的 /var/log/nginx。
提醒：挂载时必须保证映射双方的内容一致，www和logs初始都是空目录，所以不用预先copy，nginx.conf必须预先copy，因为容器内非空；
## php容器命令

## mySQL容器命令
