## 一、帮助指令

### 1.docker version:查看版本号

### 2.docker info:查看更详细消息

### 3.docker --help:常用帮助指令

<br>

## 二、镜像命令

### 1.docker images(列出本地所有镜像)

##### 1).命令:docker images [OPTIONS]

REPOSITORY:表示镜像的仓库

TAG:镜像的标签

IMAGE ID:镜像ID

CREATED:镜像创建时间

SIZE:镜像大小

##### 2).OPTIONS说明

-a:列出本地所有镜像(含中间映像层)

-q:只显示镜像ID

--digests:显示镜像的摘要信息

--no-trunc:显示完整的镜像信息

### 2.docker search 镜像名字(查询镜像)

##### 1).网站

https://hub.docker.com

##### 2).命令:docker search [OPTIONS] 镜像名字

NAME:名字

DESCRIPTION:描述

STARS: 收藏数

OFFICIAL:官方

AUTOMATED:自动的

##### 3).OPTIONS说明

--no-trunc:显示完整的镜像信息

-s:列出收藏数不小于指定值大的镜像

--automated:只列出自动构建类型镜像

```shell
docker search -s 30 --automated tomcat
```

### 3.docker pull 镜像名字(下载镜像)

```shell
docker pull tomcat #等价于docker pull tomcat:latest
```

### 4.docker rmi -f 镜像名/镜像id(删除镜像)

##### 1).删除单个

docker rmi -f 镜像名

##### 2).删除多个

docker rmi -f 镜像名1:Tag 镜像名2:Tag

```
docker rmi -f hello-world nginx
```

##### 3).删除全部

docker rmi -f $(docker images -qa)

<br>

## 三、容器命令

### 1.新建并启动当前容器(启动交互式容器)

##### 1).命令

docker run [OPTIONS] 镜像名/镜像id

##### 2).OPTIONS

--name 容器新名字:为容器指定名字

-i:以交互模式运行容器

-t:为容器重新分配一个伪终端

-d:后台运行容器,并返回容器ID,即启动守护式容器

```shell
docker run -it --name mycentos1 centos
```

### 2.列出当前所有正在运行的容器

##### 1).命令

docker ps [OPTIONS]

##### 2).OPTIONS

-a:列出当前正在运行的容器+历史上运行过得

-n:显示最近n个创建的容器

-l:显示最近创建的容器

--no-trunc:不截断输出

```shell
docker ps -n 3
```

### 3.退出容器

##### 1).命令

exit:容器停止退出

ctrl+P+Q:容器不停止退出

### 4.启动容器

##### 1).命令

docker start 容器名/容器ID

### 5.重启容器

##### 1).命令

docker restart 容器名/容器ID

### 6.停止容器

##### 1).命令

普通停止:docker stop 容器名/容器ID

强制停止:docker kill 容器名/容器ID

### 7.删除容器

##### 1).命令

删除已停止容器:docker rm 容器名/容器ID

删除未停止容器(强制删除):docker rm  -f 容器名/容器ID

删除多个容器:docker rm -f $(docker ps -qa) 或者 docker ps -qa | xargs docker rm

### 8.重要

##### 1).启动守护式容器

docker run -d 容器名/容器ID

注:会自杀，因为没有工作

```shell
docker run -d centos /bin/sh -c "while true;do echo hello world;sleep 2;done"
```

##### 2).查看容器日志

docker logs -f -t --tail 容器名/容器ID

-t:加入时间戳

-f:跟随最新的日志打印

--tail 数字:显示最后几条

```shell
docker logs -f -t --tail 3 151260356b61
```

##### 3).查看容器内运行的进程

docker top 容器名/容器ID

##### 4).查看容器内部细节

docker insepct 容器名/容器ID

##### 5).进入正在运行的容器并以命令进行交互

dokcer attach 容器名/容器ID

注:直接进入容器终端，不会启动新的进程

docker exec it 容器名/容器ID bashShell

注:在容器中打开新的终端，并且可以启动新的进程

```shell
docker attach 151260356b61 等价于 docker exec it 151260356b61 /bin/bash

docker exec -t c045a0b4c44b ls -l /tmp #不进去容器进行操作
```

##### 6).容器内文件拷贝至主机

docker cp 容器名/容器ID:容器内路径 目的主机路径

```shell
docker cp c045a0b4c44b:/tmp/yum.txt /root/abc.txt
```