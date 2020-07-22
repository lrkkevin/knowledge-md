[TOC]



### 一、rpm包的方式来安装

##### 1、下载地址

[**https:****//download.docker.com/linux/centos/7/x86_64/stable/Packages/**](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)

##### 2、下载如下文件
docker-ce-17.03.0.ce-1.el7.centos.x86_64.rpm 
docker-ce-selinux-17.03.0.ce-1.el7.centos.noarch.rpm

##### 3、安装docker
安装本地包命令: yum localinstall *

##### 4、启动docker命令
```
[root@VM_0_7_centos docker]#  systemctl start docker
[root@VM_0_7_centos docker]# ps -ef | grep docker
root      8872     1  0 14:07 ?        00:00:00 /usr/bin/dockerd
root      8875  8872  0 14:07 ?        00:00:00 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --metrics-interval=0 --start-timeout 2m --state-dir /var/run/docker/libcontainerd/containerd --shim docker-containerd-shim --runtime docker-runc
root      8986  1017  0 14:07 pts/0    00:00:00 grep --color=auto docker
[root@VM_0_7_centos docker]# 
```

##### 5、测试docker是否能成功运行
```
[root@VM_0_7_centos docker]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete 
Digest: sha256:d58e752213a51785838f9eed2b7a498ffa1cb3aa7f946dda11af39286c3db9a9
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
**到此Docker安装完成**

### 二、centos7离线安装docker-compose

在线安装可参考官网文档：https://docs.docker.com/compose/install/#install-compose

##### 1、下载安装包

下载地址：https://github.com/docker/compose/releases

##### 2、进入如下目录，对文件重命名，然后赋予执行权限
```
cd  /usr/local/bin
mv  docker-compose-Linux-x86_64  docker-compose
sudo chmod 777 docker-compose
```

##### 3、查看docker-compose版本号
```
[root@VM_0_7_centos bin]# docker-compose --version
docker-compose version 1.26.0, build d4451659
```
到此docker-compose安装成功

