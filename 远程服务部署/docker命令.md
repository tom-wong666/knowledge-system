# docker命令集合

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
docker exec -it [容器ID] /bin/bash // 进入某个container容器
exit // 退出container容器
