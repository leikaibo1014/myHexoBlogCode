---

title: Docker容器化
date: 2019-01-02 11:20:32
tags: [Docker容器化,环境配置]
categories: 环境配置
toc: true

---

# 1. Docker简介

## 1.1 什么是Docker


- 从传统意思上来讲也是一种虚拟化技术。	


- Docker项目的目标是实现一种轻量级的操作系统解决方案。Docker的基础是Linux容器LXC等技术。Docker方便了环境的部署和安装。

<!-- more -->

**Docker的优点：**  


- 如果使用Docker 只需要把别人安装好的镜像拉去下来就可以直接使用，上手方便。


- 鼓励使用面向服务的架构，绝大多数的微服务都是通过Docker来进行部署的。


- 传统的虚拟机我们需要分配本机的硬件配置资源，在本机上配置虚拟机的数量是有限制的。


- 但是使用Docker是共享我们主机的资源。

## 1.2 Docker组件
1. 客户端和服务器端  

&nbsp;&nbsp;&nbsp;&nbsp;Docker是一个客服端服务器架构CS架构程序，Docker客户端只需要向Docker服务器或者是守护进程发出请求，服务器或者是守护进程将完成所有工作并返回结果。Docker提供了一套命令行工具以及RESTful API、你可以在任意一台宿主主机上运行Docker守护进程和客户端。  
&nbsp;&nbsp;&nbsp;&nbsp;Docker守护进程就是Docker的服务端，主要是管理Docker容器的。Docker客户端主要是为了连接Docker的守护进程，通过客户端来进行操作Docker容器。

2. Docker镜像和容器

镜像是构建Docker的基石，用户基于镜像来运行自己的容器。镜像也是Docker生命周期中的构建部分。例如我们可以通过一个MySQL镜像来创建多个MySQL容器。
镜像相当于我们创建的类，容器相当于我们通过类创建的对象。

# 2. 安装docker
在CentOS下安装Docker  
（1）首先yum包更新到最新  

    Sudo  yum  update

（2）安装需要的软件包，
yum-util 提供 yum-config-，anager功能，另外两个是devicemapper驱动依赖的

    Sudo  yum  install -y yum-utils  device-mapper-persistent-data  lvm2
（3）设置yum源为阿里云

    Sudo yum-config-manager  --add-repo  http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

（4）安装docker

    Sudo yum install  docker-ce
（5）安装后查看docker的版本

    Docker  -v


**设置USTC的镜像**  
 USTC是老牌的Linux镜像服务的提供者了。USTC docker mirror 的优势之一就是不需要注册，值真正的公共服务。
编辑该文件：

    Vi /etc/docker/daemon.json

在该文件中输入如下内容：

    {
    “registry-mirrors”:[“https://docker.mirror.ustc.edu.cn”]
    }

启动Docker

    Systemctl start docker

停止Docker

    Systemctl stop docker
重启docker

    Systemctl  restart docker
查看docker的状态

    systemctl status docker
设置开机自启动

    Systemctl  enable  docker

# 3. 镜像相关的命令

查看镜像   docker  images 可以查看我们已经安装好的镜像

搜索镜像  docker search centos    我们可以基于搜索的结果进行镜像的下载

针对搜索的结果各个属性的名称进行说明  
Name是仓库名称，  
DESCRIPTION:镜像描述    
STARS:用户的评价    
OFFICIAL：是否是官方提供的镜像。  
ATTOMATED  是否是docker hub 自动创建流程所创建的


**拉取镜像：**
拉取镜像就是从中央仓库中下载镜像到本地

    docker pull 镜像名称
例如需要下载centos7镜像 

    docker pull centos：7

删除镜像：

按照镜像id进行删除镜像

    Docker rmi 镜像ID

删除所有的镜像

    Docker rmi  `docker images -q`  (注意这里不是单引号)

#4. 容器相关的命令

    [root@iZwz92dpr9gpgasbo4gg15Z lib]# docker ps
可以查看运行中的容器

创建与启动容器

    创建容器命令  docker run
    -i ：表示运行容器
    -t ：表示容器启动后会进入命令行。加入这两个参数后，容器创建就能登录进去，即分配一个伪终端。
    --name   ：为创建的容器命名
    -v ：表示目录映射

（1）交互式方式创建容器

    Docker run -it --name=容器名称  镜像名称：标签/bin/bash
创建好后我们可以通过ps命令查看，可以看到启动的容器。

    Docker run -i -t --name=mycentos centos:7 /bin/bash
    
    [root@iZwz92dpr9gpgasbo4gg15Z ~]# docker  run -i -t --name=mycentos centos /bin/bash
    [root@e4202738a947 /]# ^C
    [root@e4202738a947 /]# 

这个时候我们可以发现，前面的名称不一样了，说明我们现在已经进入centos这个容器中了

我们查看目录，也可以发现和宿主机的目录结构一样，说明我们是在宿主机的基础上又虚拟了一台服务器。  
我们重新打开窗口可以看到

    Docker ps 命令
    [root@iZwz92dpr9gpgasbo4gg15Z lib]# docker ps
    CONTAINER IDIMAGE   COMMAND CREATED STATUS  PORTS   NAMES
    e4202738a947centos  "/bin/bash" 4 minutes ago   Up 4 minutesmycentos

可以对docker的运行状态进行查看。
我们在docker虚拟机上运行exit 然后我们就退回到宿主机上了  
退出宿主机之后，docker是运行还是关着的？然后我们通过ps命令查看，发现容器处于关闭状态。  
**结论：如果我们通过交互式的命令来创建容器，当我们使用exit命令退出之后，随之的容器也进行自动关闭状态。  **

（2）以守护式方式来创建容器

    docker run -di --name=容器名称  镜像名称：标签

登录守护式容器方式：

    Docker exec -it 容器名称（或者是容器ID） /bin/bash
    
    [root@iZwz92dpr9gpgasbo4gg15Z ~]# docker run -di --name=mycentos2 centos
    4a7d23fda453c40b61138e63753e84833069fd79a9af82de03f6ceab52d50e16
    [root@iZwz92dpr9gpgasbo4gg15Z ~]# 
出现字符串说明容器创建成功。
我们的命令行还是处于宿主机的状态，这个时候我们通过docker ps命令来查看容器状态

**如何进入容器呢？**  

    ocker exec -it 容器名称 /bin/bash
    
    Docker exec -it mycentos2  /bin/bash
    [root@iZwz92dpr9gpgasbo4gg15Z ~]# docker exec -it mycentos2 /bin/bash
    [root@4a7d23fda453 /]# ^C

这个时候如果我们通过exit命令退出容器之后，然后通过docker ps命令查看所有运行的容器，我们可以发现，容器还是处于运行状态。

这就是两种创建方式的区别

## 4.1停止与启动容器


停止容器：

    Docker stop 容器名称（或者容器ID）
启动容器：

	docker start 容器名称（或者容器ID） 
	
	[root@iZwz92dpr9gpgasbo4gg15Z ~]# docker ps -a
	CONTAINER IDIMAGE   COMMAND CREATED STATUS  PORTS   NAMES
	4a7d23fda453centos  "/bin/bash" 8 minutes ago   Up 8 minutesmycentos2
	e4202738a947centos  "/bin/bash" 21 minutes ago  Exited (1) 15 minutes ago   mycentos
	[root@iZwz92dpr9gpgasbo4gg15Z ~]# docker stop 4a7d23fda453 


## 4.2 文件拷贝


有时候我们需要把文件或者是目录拷贝进容器中，或者是把容器中的文件拷贝出来。
文件拷贝进容器命令cp  

    docker cp 需要拷贝的文件或者目录  容器名称：容器目录
也可以将文件从容器中拷贝出来

    docker cp 容器名称：容器目录  需要拷贝的文件或目录

都是在宿主机上户进行操作


## 4.3 目录挂载

我们进行上面文件拷贝的时候操作容器中的或者是宿主机中的内容时，有事不是很方便的操作，这个时候我们可以通过目录挂载的方式进行操作。  

我们可以在创建容器的时候，将宿主机的目录与容器内的目录进行映射，这样我们就可以通过修改宿主机中的某个目录文件从而去影响容器。  
创建容器  添加-v参数  后边为宿主机目录：容器目录  例如  

    Docker run -di -v /usr/local/myhtml :/usr/local/myhtml --name=mycentos3 centos

注意：如果你共享的是多级目录，可能会出现权限不足的提示
这是因为centos中的安全模式selinux把权限禁掉了，我们需要添加参数 --privileged=true 来解决挂载的目录没有权限的问题。

## 4.4 查看容器IP地址

我们可以通过以下命令来查看容器运行中的各种数据


`Docker inspect 容器名称 （容器ID）`  

也可以直接执行下面的命令直接输出IP地址

    Docker  inspect --format=’{{.NetworkSettings.IPAddress}}’ 容器名称 （容器ID）


## 4.5 删除容器
Docker   rm 容器名称（容器ID）  

如果容器是正在运行中无法进行删除。

# 5. 应用部署

##5.1 MySQL部署
（1）拉取MySQL镜像  

    Docker pull  centos/mysql-57-centos7
（2）创建容器

    Docker run -di --name=tensquare_mysql -p 33306:3306  -e  MYSQL_ROOT_PASSWORD=123456 mysql
- -p ：代表端口映射吗，格式为宿主机映射端口：容器运行端口  
- -e ：代表添加环境变量，MYSQL_ROOT_PASSWORD是root用户的登录数据库的密码  

（3）进入mysql容器  

    Docker exec -it tensquare_mysql /bin/bash
（4）登录mysql  

    Mysql -u root -p

创建mysql容器：  

    [root@iZwz92dpr9gpgasbo4gg15Z local]# docker run -di --name=tensquare_mysql -p 33306:3306  -e  MYSQL_ROOT_PASSWORD=123456 centos/mysql-57-centos7
    0babaf900514d06b02ffdec0a51ca1ad32d7e2cb7f8753b804740502ad95c764
    [root@iZwz92dpr9gpgasbo4gg15Z local]# docker ps -a

我们可以通过宿主机的端口映射，通过访问宿主机的方式来进行访问容器

## 5.2 Tomacat部署

（1）拉取镜像  

    Docker pull tomcat：7-jre7

（2）创建容器  
创建容器  -p表示地址映射

    Docker run  -di --name=mytomcat  -p 9000:8080
    -v /usr/local/webapps:/usr/local/tomcat/webapps  tomcat:7-jre7
这里为了项目很好的部署我们可以做一个项目映射主机usr/local/webapps映射容器内的usr/local/tomcat/webapps   如果没有文件会自动创建

## 5.3 Nginx部署

（1）拉取镜像  

    Docker pull nginx
（3）创建nginx容器

    Docker run -di --name=mynginx -p 80:80 nginx

## 5.4  Redis部署
（1）拉取镜像  

    Docker pull redis
（2）创建容器

    Docker run -di --name=myredis -p 6379:6379  redis

# 6. 迁移和备份

## 6.1 容器保存为镜像
我们可以通过以下命令将容器保存为镜像

    Docker commit mynginx mynginx_i
    mynginx是容器名称 mynginx_i表示镜像名称要保存为的镜像。

## 6.2 镜像备份
我们可以通过以下命令将镜像保存为tar文件。可以在另一台机器上进行部署

    Docker save -o  mynginx.tar  mynginx_i  
    mynginx_i  表示镜像的名称

我们在另一台机器上可以进行压缩文件转化为镜像

    Docker  load -i mynginx.tar文件名称   这个时候就可以把文件恢复成镜像


# 6. Dockerfile
Dockerfile是由一系列命令和参数构成的脚本，这些命令应用于基础镜像并最终创建一个新的镜像。基础镜像一般是系统级的镜像，比如我们可以在centos镜像的基础上再配置其他的镜像。我们构建镜像一般是在一个镜像的基础上创建另一个镜像。

1. 对于开发人员：可以为开发团队提供一个完全一致的开发环境
1. 对于测试人员：可以直接拿开发时所构建的镜像或者通过dockerfile文件构建一个新的镜像开始工作了
1. 对于运维人员：在部署时，可以实现应用的无缝移植


## 6.1 常用命令

| 命令  | 作用  |
|-----|-----|
|FROM image_name.tag|定义了使用哪个基础镜像构建流程|
|MAINTAINER  user_name|声明镜像的创建者|
|ENV key value|设置环境变量（可以写多条）|
|RUN command|是Dikerfile的核心部分（可以写多条）|
|ADD source_dir/file dest_dir/file|将宿主机的文件复制到容器内，如果是一个压缩文件，将会在复制后自动解压|
|COPY source_dir/file dest_dir/file|和ADD相似，但是如果有压缩文件并不能进行压缩|
|WORKDIR path_dir|设置工作目录|

下面我们通过dockerfile来构建一个镜像，jdk的镜像 使用的基础镜像是centos7镜像  

    首先创建一个文件
    [root@iZwz92dpr9gpgasbo4gg15Z ~]# mkdir -p /usr/local/dockerjdk8
 我们在这个文件下进行dockerfile 来创建一个镜像，并把jdk8的压缩包也放进这个文件下  
使用ftp上传工具把jdk压缩包放进这个文件夹下

    sftp:/usr/local/mytestdocker> put E:\CHDEvaluation.war
    然后把jdk压缩包放进dockerjdk8这个文件下

然后创建文件，这个名称必须是Dockerfile  D是大写的
    [root@iZwz92dpr9gpgasbo4gg15Z dockerjdk8]# vi Dockerfile
    Dockerfile

我们可以在Dockerfile文件中增加以下内容：


    FROM centos:7   		#基础镜像
    MAINTAINER leikaibo 	#作者名称
    WORKDIR /usr			
    RUN mkdir /usr/local/javatest
    ADD jdk-8u191-linux-i586.tar.gz /usr/local/javatest  #把我们上传的文件添加到目录中
    # 以下是设置环境变量
    ENV JAVA_HOME=/usr/local/javatest/jdk1.8.0_191
    ENV JRE_HOME=$JAVA_HOME/jre
    ENV CLASSPATH $JAVA_HOME/bin/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
    ENV PATH $JAVA_HOME/bin:$PATH
    ~   

然后执行命令

    docker build -t='jdk1.8' .   -t代表指定镜像的名称   .代表去当前目录中去找Dockerfile文件
执行完之后，我们可以通过docker images来查看镜像


    [root@iZwz92dpr9gpgasbo4gg15Z dockerjdk8]# docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
jdk1.8                    latest              baa98f17d615        16 seconds ago      599MB
nginx                     latest              e445ab08b2be        2 weeks ago         126MB
tomcat                    7-jre7              47c156f4d4e3        2 months ago        359MB
centos/mysql-57-centos7   latest              e35b3f7a4ea0        4 months ago        452MB
centos                    7                   9f38484d220f        4 months ago        202MB
centos                    latest              9f38484d220f        4 months ago        202MB


