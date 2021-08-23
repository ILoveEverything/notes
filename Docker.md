Docker

## Docker常用命令-基础操作

### 镜像操作

#### 1 查看Docker信息

- `docker info`

#### 2 本地docker配置

##### 存放位置

- `/etc/docker/daemon.json`

##### 存放内容

```json
{
  "__储存驱动__": "重要",
  "storage-driver": "overlay2",
  "features": {
    "__Docker镜像构建器__": "重要",
    "buildkit": true
  },
  "__守护进程更改为调试模式__": "重要",
  "debug": true,
  "__在守护进程停机期间使容器保持活动状态__": "重要",
  "live-restore": true,
  "__如果在容器创建时未指定，它将更新要使用的运行时。它默认为“default”，这是官方 docker 软件包附带的运行时。__": "",
  "default-runtime": "runc",
  "__更新可用于运行容器的可用 OCI 运行时列表__": "",
  "runtimes": {
    "cc-runtime": {
      "path": "/usr/bin/cc-runtime"
    }
  },
  "__指定要使用的授权插件__": "重要",
  "authorization-plugins": [],
  "--Docker工作目录__": "",
  "graph": "/home/docker",
  "__私有仓库使用__": "重要",
  "insecure-registries": [],
  "__Docker网络地址__": "重要",
  "bip": "172.7.0.62/24",
  "__Docker启动时的一些额外的参数__": "重要",
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```

#### 3 查看`Docker Hub`上的镜像

- `docker search 镜像名`

#### 4 拉取`Docker Hub`上的镜像

- `docker pull 镜像名`

#### 5 查看本地镜像

- `docker images`或`docker image ls`

#### 6 运行镜像(启动容器)

本地有这个镜像,则运行本地镜像,若本地没有此镜像则从`Docker Hub`上拉取镜像运行

- `docker run [OPTIONS] 镜像名 [COMMAND][ARG...]`

`OPTIONS`选项:

- `-i`:表示启动一个可交互的容器,并持续打开标准输入
- `-t`:表示使用终端关联到容器的输入输出上(通常和`-i`配合使用)
- `-d`:非交互式启动一个容器(后台启动)
- `--rm`:退出后即删除容器
- `--name`:表示定义容器的唯一名称

将运行的结果导出到日志文件不在终端显示(其中`2>$1`表示输出不在当前系统-->输出到宿主机)

- `docker run 镜像名 2>&1 >> 指定文件目录`

#### 7 给镜像打标签

- `docker tag 镜像id 镜像名:版本号`

#### 8 删除镜像

##### 通过镜像名删除

- `docker rmi 镜像名`

##### 通过镜像id删除

- `docker rmi 镜像id`

若镜像被多个标签使用,需强制删除时

- `docker rmi -f 镜像id`

### 容器操作

#### 1 查看容器

查看未退出的容器

- `docker ps`

查看全部容器,可使用参数`-a`查看

- `docker ps -a`

#### 2 删除容器

- `docker rm 容器id(或容器名)`

指定在多久之后删除容器

- `docker rm -t 时长(单位:秒) 容器id(或容器名)`

强制删除一个正在运行的容器

- `docker rm -f 容器id(或容器名)`

#### 3 进入一个正在运行的容器

通过容器id以交互状态进入容器

- `docker exec -i -t 容器id(或容器名) /bin/sh`

#### 4 启动容器

- `docker start 容器id(或容器名)`

#### 5 停止容器

- `docker stop 容器id(或容器名)`

#### 6 重启容器

- `docker restart 容器id(或容器名)`

#### 7 提交容器(使容器保存为一个新的镜像)

- `docker commit -p 容器id(或容器名) 新镜像名:新镜像版本`

#### 8 导出镜像

- `docker save 镜像id > 新镜像名.tar`

#### 9 导入镜像

- `docker load < 镜像名`

导入的镜像是没有标签的,可以使用标签命令给镜像赋标签`docker tag 镜像id 镜像名:版本号`

#### 10 查看日志

- `docker logs 容器id`

日志动态输出

- `docker logs -f 容器id`

### 脚本

#### 删除已退出的容器

- ```sh
  for i in `docker ps -a | grep -i exit |awk '{print $1}'`;do docker rm -f $i;done
  ```

#### 重启已退出的容器

- ```sh
  for i in `docker ps -a | grep -i exit |awk '{print $1}'`;do docker restart $i;done
  ```

#### 启动已退出的容器

- ```sh
  for i in `docker ps -a | grep -i exit |awk '{print $1}'`;do docker start $i;done
  ```

#### 停止已启动的容器

- ```sh
  for i in `docker ps -a | grep -i up |awk '{print $1}'`;do docker stop $i;done
  ```

## Docker常用命令-高级操作

### 1 映射端口

- `dokcer run -p 容器外端口:容器内端口`

`-P`为映射到宿主机的随机一个端口

### 2 挂载数据卷

- `docker run -v 容器外目录:容器内目录`

### 3 传递环境变量

- `docker run -e 环境变量key=环境变量value -e 环境变量key=环境变量value ...`

### 4 容器内安装软件(工具)

- `yum/apt-get/get/pacman`

## 容器的生命周期

- 检查本地是否存在镜像,如果不存在则从远端仓库检索
- 利用镜像启动容器
- 分配一个文件系统,并在只读的镜像层外挂在一层可读写层
- 从宿主机配置的网桥接口中桥接一个虚拟接口到容器
- 从地址池配置一个ip地址给容器
- 执行用户指定的指令
- 执行完毕后容器终止

![image-20210803165147800](/home/xiejinwu/notes/images/image-20210803165147800.png)

### Dockerfile的规则

- 格式
  - `#`为注释
  - 指令(大写)内容(小写)
- Docker是按顺序执行Dockerfile里的指令合集的(从上到下执行)
- 每一个Dockerfile的第一个非注释行指令,必须是`FORM`指令,用于为镜像文件构建过程中,指定基准镜像,后续的指令运行于此基准镜像所提供的运行环境中
  - 实践中,基准镜像可以是任何可用镜像文件,默认情况下`docker build`会在docker主机(本地)上查找指定的镜像文件,当其在本地不存在时,则会从Docker registry(远端)上拉去所需的镜像文件

### 4组核心的Dockerfile指令

`Dockerfile`构建命令:

```sh
docker build . -t 新镜像名:版本号
```

其中`.`表示当前目录,`-t`为打标签

#### USER/WORKDIR指令

- `USER`-->指定基准镜像的使用用户

- `WORKDIR`-->切换目录,类似于`Linux`中的`cd`命令

```dockerfile
FROM nginx:latest
USER root
WORKDIR /root
```

#### ADD/EXPOSE指令

- `ADD`-->将本地文件固化到Docker镜像中
- `EXPOSE`-->规定镜像对外暴露的端口号(只有和`-P`一起使用才生效,和`-p`一起使用是不生效的)

```dockerfile
FROM nginx:latest
ADD index.html /usr/share/nginx/html/innginx-testdex.html
EXPOSE 80
```

#### RUN/ENV指令

- `RUN`-->构建镜像时执行的命令
- `ENV`-->构建时加入的环境变量

```dockerfile
FROM archlinu:latestx
RUN rm /etc/pacman.conf /etc/pacman.d/mirrorlist
ADD pacman.conf /etc/pacman.conf
ADD mirrorlist /etc/pacman.d/mirrorlist
ENV export home="/home"
```

#### CMD/ENTRYPOINT指令

- `CMD`-->启动容器时执行的命令
- `ENTRYPOINT`-->容器启动后执行的脚本文件,类似`ADD`的进阶命令

## Docker网络模型

- `NAT`(默认)
- `None`(不对外提供网络接口)
- `Host`(Docker网络与宿主机网络保持一致)
- 联合网络(共享网络空间)

