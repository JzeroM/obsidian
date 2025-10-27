## 查看版本信息
```shell
docker version
```
## 显示统信息，包括镜像和容器数
```shell
docker info
```
## 帮助命令
```shell
docker --help
```
## 查看镜像 （docker images -a 含中间镜像层）
```shell
docker images
```
## 镜像 ID 删除单个镜像
```shell
docker rmi -f 
```
## 容器 ID 删除单个容器
```shell
docker rm
```
## 镜像名:版本号拉取镜像
```shell
docker pull
```
## 启动镜像
```shell
docker run -it REPOSITORY /bin/bash
```
--name="容器新名字": 为容器指定一个名称；

-d: 后台运行容器，并返回容器ID，也即启动守护式容器；

-i：以交互模式运行容器，通常与 -t 同时使用；

-t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；

-P: 随机端口映射；

-p: 指定端口映射；
## 查看正在运行容器
```shell
docker ps
```
## 查看所有容器
```shell
docker ps -a
```
## 容器 ID 或者容器名启动容器
```shell
docker start 
```
## 容器 ID 或者容器名重启容器
```shell
docker restart
```
## 容器 ID 或者容器名停止容器
```shell
docker stop
```
## 容器 ID 或者容器名强制停止容器
```shell
docker kill
```
## 停用全部运行中的容器
```shell
docker kill $(docker ps -a -q) 
```
## 删除全部容器
```shell
docker rm $(docker ps -aq)
```
## 删除所有镜像
```shell
docker rmi docker images -q
```
## 显示所有容器信息
```shell
docker container ls -a 
```
## 查看 docker 网络
```shell
docker network ls
```
## 查看指定 docker 网卡
```shell
docker network inspect 网卡id
```
## 删除 docker 网卡
```shell
docker network rm 网卡id
```
## 查看 docker0
```shell
ip addr show docker0
```
