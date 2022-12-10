---
title: docker
date: 12-07 8.23 
categories: 杂货技术栈
comments: "true"
---
# 简介

这个是关于docker的简单介绍以及使用

本来这个我其实不打算写的因为网上关于docker的教程很多，而且都比较全，我目前所学的全部都是基于菜鸟教程的

# 概念

首先我们要明白，什么是docker

docker就是相当于一个箱子，其里面有它自己的生态圈

各种环境依赖是直接现成的那样，和之前java打包成exe文件后面绑定依赖是一样的

# 为什么现在docker会会很火

因为docker不需要我们配置复杂的环境变量，只要我们通过网络下载一个包含这个功能的linux或者unbanto镜像就行

特别方便，不过方便的同时也会带来隐患，比如，不知道原生安装的话，我们如何详细的知道这个组件的功能？

## docker的安装

docker支持多种操作系统的安装，以下我只简单介绍关于linux和云服务器的安装方法

### linux

使用官方命令安装

`curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun`

也可以用国内的daocloud安装

`curl -sSL https://get.daocloud.io/docker | sh`

这样在有网的机器上就安装完成了，是不是很简单 ，

接下来我们要说手动安装的情况

首先要卸载旧版本

较旧的 Docker 版本称为 docker 或 docker-engine 。如果已安装这些程序，请卸载它们以及相关的依赖项。

```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

```

在新主机上首次安装 Docker Engine-Community 之前，需要设置 Docker 仓库。之后，您可以从仓库安装和更新 Docker。

安装所需的软件包。yum-utils 提供了 yum-config-manager ，并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2。

```
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

使用以下命令来设置稳定的仓库。

官方地址源

```
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

阿里云地址源

```
$ sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

清华大学的

```
$ sudo yum-config-manager \
    --add-repo \
    https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
```

在国内还是建议阿里和清华大学的

安装最新版本的 Docker Engine-Community 和 containerd，或者转到下一步安装特定版本：

`$ sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin`

如果提示您接受 GPG 密钥，请选是。

Docker 安装完默认未启动。并且已经创建好 docker 用户组，但该用户组下没有用户。

**要安装特定版本的 Docker Engine-Community，请在存储库中列出可用版本，然后选择并安装：**

```
$ yum list docker-ce --showduplicates | sort -r

docker-ce.x86_64  3:18.09.1-3.el7                     docker-ce-stable
docker-ce.x86_64  3:18.09.0-3.el7                     docker-ce-stable
docker-ce.x86_64  18.06.1.ce-3.el7                    docker-ce-stable
docker-ce.x86_64  18.06.0.ce-3.el7                    docker-ce-stable
```

通过其完整的软件包名称安装特定版本，该软件包名称是软件包名称（docker-ce）加上版本字符串（第二列），从第一个冒号（:）一直到第一个连字符，并用连字符（-）分隔。例如：docker-ce-18.09.1。

`$ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io`

然后启动docker

`$ sudo systemctl start docker`

然后运行hello world镜像查看是不是我们成功安装了这个docker

`$ sudo docker run hello-world`

### 卸载docker

先删除安装包

`yum remove docker-ce `

然后删除镜像文件等

`rm -rf /var/lib/docker`

云服务器和上面一样

# docker 命令

docker命令的种类不多但是其中的分支较多

docker run : 原本的意义是创建一个docker容器，并运行它

```
-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；

-d: 后台运行容器，并返回容器ID；

-i: 以交互模式运行容器，通常与 -t 同时使用；

-P: 随机端口映射，容器内部端口随机映射到主机的端口

-p: 指定端口映射，格式为：主机(宿主)端口:容器端口

-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

--name="nginx-lb": 为容器指定一个名称；

--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；

--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；

-h "mars": 指定容器的hostname；

-e username="ritchie": 设置环境变量；

--env-file=[]: 从指定文件读入环境变量；

--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；

-m :设置容器使用内存最大值；

--net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；

--link=[]: 添加链接到另一个容器；

--expose=[]: 开放一个端口或一组端口；

--volume , -v: 绑定一个卷
```


**docker start** :启动一个或多个已经被停止的容器

**docker stop** :停止一个运行中的容器

**docker restart** :重启容器

**docker kill** :杀掉一个运行中的容器。

* **-s :** 向容器发送一个信号

**docker rm ：** 删除一个或多个容器。

```
-f :通过 SIGKILL 信号强制删除一个运行中的容器。

-l :移除容器间的网络连接，而非容器本身。

-v :删除与容器关联的卷。
```

* 命令可以嵌套使用如下 ：
  * `删除所有已经停止的容器：docker rm $(docker ps -a -q)`


**docker pause** :暂停容器中所有的进程。

**docker unpause** :恢复容器中所有的进程。

**docker create ：** 创建一个新的容器但不启动它 ：其语法和run一样

**docker exec ：** 在运行的容器中执行命令

```
-d :分离模式: 在后台运行

-i :即使没有附加也保持STDIN 打开

-t :分配一个伪终端
```

docker ps : 列出容器

```
-a :显示所有的容器，包括未运行的。

-f :根据条件过滤显示的内容。

--format :指定返回值的模板文件。

-l :显示最近创建的容器。

-n :列出最近创建的n个容器。

--no-trunc :不截断输出。

-q :静默模式，只显示容器编号。

-s :显示总的文件大小。
```

输出介绍

```
输出详情介绍：

CONTAINER ID: 容器 ID。

IMAGE: 使用的镜像。

COMMAND: 启动容器时运行的命令。

CREATED: 容器的创建时间。

STATUS: 容器状态。

状态有7种：

created（已创建）
restarting（重启中）
running（运行中）
removing（迁移中）
paused（暂停）
exited（停止）
dead（死亡）
PORTS: 容器的端口信息和使用的连接类型（tcp\udp）。

NAMES: 自动分配的容器名称。
```

**docker inspect :** 获取容器/镜像的元数据。

```
-f :指定返回值的模板文件。

-s :显示总的文件大小。

--type :为指定类型返回JSON。
```

**docker top :** 查看容器中运行的进程信息，支持 ps 命令参数。

`docker top [OPTIONS] CONTAINER [ps OPTIONS]`

* 查看所有运行容器的进程信息。
* `for i in  `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done`

**docker attach :** 连接到正在运行中的容器。

要attach上去的容器必须正在运行，可以同时连接上同一个container来共享屏幕（与screen命令的attach类似）。

docker events : 从服务器获取实时事件

```
-f ：根据条件过滤事件；

--since ：从指定的时间戳后显示所有事件;

--until ：流水时间显示到指定的时间为止；

如果指定的时间是到秒级的，需要将时间转成时间戳。如果时间为日期的话，可以直接使用，如--since="2016-07-01"。
```

docker logs : 获取容器的日志

```
-f : 跟踪日志输出

--since :显示某个开始时间的所有日志

-t : 显示时间戳

--tail :仅列出最新N条容器日志
```

**docker wait :** 阻塞运行直到容器停止，然后打印出它的退出代码

**docker export :** 将文件系统作为一个tar归档文件导出到STDOUT。

```
-o :将输入内容写到文件。
```

docker port 用于列出指定的容器的端口映射，或者查找将 PRIVATE_PORT NAT 到面向公众的端口。

```
docker port [OPTIONS] CONTAINER [PRIVATE_PORT[/PROTO]]
```

docker stats : 显示容器资源的使用情况，包括：CPU、内存、网络 I/O 等。

```
--all , -a :显示所有的容器，包括未运行的。

--format :指定返回值的模板文件。

--no-stream :展示当前状态就直接退出了，不再实时更新。

--no-trunc :不截断输出。
```

**docker commit :** 从容器创建一个新的镜像。

```
-a :提交的镜像作者；

-c :使用Dockerfile指令来创建镜像；

-m :提交时的说明文字；

-p :在commit时，将容器暂停。
```

**docker cp :** 用于容器与主机之间的数据拷贝。

```
-L :保持源目标中的链接
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
例子 ：
实例
将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下。

docker cp /www/runoob 96f7f14e99ab:/www/
将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www。

docker cp /www/runoob 96f7f14e99ab:/www
将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中。

docker cp  96f7f14e99ab:/www /tmp/
```

docker diff : 检查容器里文件结构的更改。

```
docker diff [OPTIONS] CONTAINER
```

**docker login :** 登陆到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

**docker logout :** 登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub

```
docker login [OPTIONS] [SERVER]
docker logout [OPTIONS] [SERVER]
-u :登陆的用户名

-p :登陆的密码
```

docker pull : 从镜像仓库中拉取或者更新指定镜像

```
-a :拉取所有 tagged 镜像
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
--disable-content-trust :忽略镜像的校验,默认开启
```

docker push : 将本地的镜像上传到镜像仓库,要先登陆到镜像仓库

```
--disable-content-trust :忽略镜像的校验,默认开启
docker push [OPTIONS] NAME[:TAG]
```

**docker search :** 从Docker Hub查找镜像

```
docker search [OPTIONS] TERM
--automated :只列出 automated build类型的镜像；

--no-trunc :显示完整的镜像描述；

-f <过滤条件>:列出收藏数不小于指定值的镜像。
```

docker images : 列出本地镜像。

```
docker images [OPTIONS] [REPOSITORY[:TAG]]
-a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；

--digests :显示镜像的摘要信息；

-f :显示满足条件的镜像；

--format :指定返回值的模板文件；

--no-trunc :显示完整的镜像信息；

-q :只显示镜像ID。
```

docker rmi : 删除本地一个或多个镜像。

```
docker rmi [OPTIONS] IMAGE [IMAGE...]
-f :强制删除；

--no-prune :不移除该镜像的过程镜像，默认移除；
```

docker tag : 标记本地镜像，将其归入某一仓库。

```
docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
```

docker build 命令用于使用 Dockerfile 创建镜像

```
docker build [OPTIONS] PATH | URL | -
--build-arg=[] :设置镜像创建时的变量；

--cpu-shares :设置 cpu 使用权重；

--cpu-period :限制 CPU CFS周期；

--cpu-quota :限制 CPU CFS配额；

--cpuset-cpus :指定使用的CPU id；

--cpuset-mems :指定使用的内存 id；

--disable-content-trust :忽略校验，默认开启；

-f :指定要使用的Dockerfile路径；

--force-rm :设置镜像过程中删除中间容器；

--isolation :使用容器隔离技术；

--label=[] :设置镜像使用的元数据；

-m :设置内存最大值；

--memory-swap :设置Swap的最大值为内存+swap，"-1"表示不限swap；

--no-cache :创建镜像的过程不使用缓存；

--pull :尝试去更新镜像的新版本；

--quiet, -q :安静模式，成功后只输出镜像 ID；

--rm :设置镜像成功后删除中间容器；

--shm-size :设置/dev/shm的大小，默认值是64M；

--ulimit :Ulimit配置。

--squash :将 Dockerfile 中所有的操作压缩为一层。

--tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。

--network: 默认 default。在构建期间设置RUN指令的网络模式
```

docker history : 查看指定镜像的创建历史。

```
docker history [OPTIONS] IMAGE
-H :以可读的格式打印镜像大小和日期，默认为true；

--no-trunc :显示完整的提交记录；

-q :仅列出提交记录ID。
```

docker save : 将指定镜像保存成 tar 归档文件

```
docker save [OPTIONS] IMAGE [IMAGE...]
-o :输出到的文件。
```

docker load : 导入使用 docker save 命令导出的镜像。

```
docker load [OPTIONS]
--input , -i : 指定导入的文件，代替 STDIN。

--quiet , -q : 精简输出信息。
```

**docker import :** 从归档文件中创建镜像。

```
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
-c :应用docker 指令创建镜像；

-m :提交时的说明文字；
```

docker info : 显示 Docker 系统信息，包括镜像和容器数。

```
docker info [OPTIONS]
```

docker version :显示 Docker 版本信息。

```
-f :指定返回值的模板文件。
```
