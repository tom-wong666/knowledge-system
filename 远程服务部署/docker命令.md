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

## php容器命令

## mySQL容器命令
