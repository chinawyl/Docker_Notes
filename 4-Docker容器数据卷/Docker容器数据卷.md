## 一、Docker容器数据卷是什么

当我们在使用docker容器的时候，会产生一系列的数据文件，这些数据文件在我们关闭docker容器时是会消失的，但是其中产生的部分内容我们是希望能够把它给保存起来另作用途的，Docker将应用与运行环境打包成容器发布，我们希望在运行过程钟产生的部分数据是可以持久化的的，而且容器之间我们希望能够实现数据共享。

##### 类似于Redis里面的rdb和aof文件

<br>

## 二、Docker容器数据卷能干嘛

### 1.容器的持久化

### 2.容器间继承+共享数据

<br>

## 三、数据卷(容器内添加)

### 1.直接命令添加

##### 1).命令

docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名

##### 2).查看数据卷是否挂载成功。

docker imspect 容器ID

注:返回的json串有这么一串数据说明挂载成功。

##### 3).容器和宿主机之间通信(数据共享)

```shell
#容器
cd dataVolumeContainer/
vi host.txt
touch container.txt

#宿主机
cd myDataVolume/
ls
```

##### 4).容器关闭后在宿主机更改文件，容器再次启动数据仍然同步

##### 5).命令(带权限)

docker run -it -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
注:此时主机能对数据卷进行更改，但是容器不能对其更改，**只允许读**

### 2.DockerFile添加

##### 1).根目录下新建mydocker文件夹并进入

```shell
touch mydocker
```

##### 2).在DockerFile中使用VOLUME指令给镜像添加一个或者多个数据卷

```shell
VOLUME["/dataVolumeContainer","dataVolumeContainer2","dataVolumeContainer3"]
```

注:出于可移植和分享的考虑，用-v命令这种方法不能够直在DockerFile中实现，由于宿主机目录是依赖于特定宿主机的，并不能保证所有的宿主机都存在这样的特定目录

##### 3).File构建

```shell
vim DockerFile

FROM centos
VOLUME ["/datavolumecontainer1","/datavolumecontainer2"] #新建两个数据卷
CMD echo "finished,------success1" #打印测试
CMD /bin/bash #等价于docker run -it -v /host:/datavolumecontainer1 -v /host2: datavolumecontainer2 centos /bin/bash:
```

##### 4).编写DockerFile文件

build后生成镜像-----获得新的镜像

```shell
docker bulid -f /mydocker/DockerFile -t wyl/centos
```

##### 5).run容器

可以看到已经创建两个数据卷，对应主机路径

```shell
#主机
docker imspect 容器ID #可以看到宿主机的数据卷路径

cd 路径
```

注:

Docker 挂载主机目录Docker出项cannot open directory .:Permission denied
解决办法：在挂载目录后面 多加一个--privileged=true参数即可

```shell
docker run -it -v /mydatavolume:/datavolumecontainer --privileged=true 镜像名
```

<br>

## 四、数据卷容器

### 1.数据卷容器是什么

命名的容器挂载数据卷，其他容器通过挂载这个容器(父容器)实现数据共享，挂载数据卷的容器，称为数据卷容器

### 2.数据卷容器总体介绍

以上一步新建的镜像为模板运行容器dc01/dc02/dc03

他们已经具有容器卷/datavolumecontainer1和/datavolumecontainer2

容器间传递共享(--volumes-from)

### 3.操作

```shell
docker run -it --name dc01 wyl/centos

docker run -it --name dc02 --volumes from dc01 wyl/centos
```

注:假设有dc01,dc02,dc03,且dc02,dc03继承dc01,即使删除dc01也没有影响,dc02和dc03依然共享