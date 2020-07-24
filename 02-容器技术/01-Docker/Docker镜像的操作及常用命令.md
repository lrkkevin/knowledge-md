[TOC]

| 修订记录   | 版本 | 是否发布 |
| ---------- | ---- | -------- |
| 2020/07/23 | v1.0 | 是       |

### 一、启动镜像

启动镜像 (因启动命令参数过多，同时各种镜像启动时可以增加额外的参数，本次以启动mysql5.6为例)
命令：
docker run -p 本机映射端口:镜像映射端口 -d --name 启动镜像名称 -e 镜像启动参数 镜像名称:镜像版本号

参数释义：
-p   本机端口和容器启动端口映射（指定端口映射）
-P   随机端口映射
-d   后台运行
-i    以交互模式运行容器
-t   为容器重新分配一个伪输入终端
--name  容器名称
-e  镜像启动参数 
-net：网络模式
例1：docker run -p 3306:3306 -d --name mysql01 -e MYSQL_ROOT_PASSWORD=admin mysql:5.6
例2：docker run -i -t --name mycentos
例3：docker run -d mycentos

启动一个或多个已经被停止的容器
docker start redis

重启容器
docker restart redis

参考官方文档: https://hub.docker.com/_/mysql

###  二、常用命令

##### 1、镜像相关命令

**提示：对于镜像的操作可使用镜像名、镜像长ID和短ID**

| 命令                              | 含义                                             | 示例               |
| --------------------------------- | ------------------------------------------------ | ------------------ |
| docker search 镜像名称            | 搜索镜像                                         | docker search java |
| docker pull java:8                | 从仓库中下载镜像，若要指定版本，则要在冒号后指定 | docker pull java:8 |
| docker pull -a java               | 下载仓库所有java镜像                             |                    |
| docker images                     | 列出已经下载的镜像                               |                    |
| docker rmi java                   | 删除本地镜像                                     |                    |
| docker rmi -f java                | 强制删除(针对基于镜像有运行的容器进程)           |                    |
| docker rmi -f redis tomcat nginx  | 多个镜像删除，不同镜像间以空格间隔               |                    |
| docker rmi -f $(docker images -q) | 删除本地全部镜像                                 |                    |
| docker build                      | 构建镜像                                         |                    |

##### 2、镜像构建
（1）编写dockerfile
cd /docker/dockerfile

（2）构建docker镜像
 docker build -f /docker/dockerfile/mycentos -t mycentos:1.1

##### 3、容器相关命令

| 命令                                                     | 含义                               | 示例                      |
| -------------------------------------------------------- | ---------------------------------- | ------------------------- |
| docker ps                                                | 查看当前启动的镜像                 | docker ps                 |
| docker stop 镜像实例ID                                   | 停止镜像                           | docker stop fe754db626db  |
| docker ps -a                                             | 查看所有镜像（包括未启动的）       | docker ps -a              |
| docker start 镜像实例ID                                  | 当镜像实例已经存在时，重新启动镜像 | docker start fe754db626db |
| docker start 镜像名称                                    | 当镜像实例已经存在时，重新启动镜像 | docker start mysql01      |
| docker rm 镜像实例ID                                     | 删除镜像实例(删除已停止的容器)     | docker rm fe754db626db    |
| docker rm -f 容器id                                      | 删除正在运行的容器                 |                           |
| docker kill 容器id                                       | 强制停止容器                       |                           |
| docker inspect 容器id                                    | 查看容器的所有信息                 |                           |
| docker container logs 容器id                             | 查看容器日志                       |                           |
| docker top 容器id                                        | 查看容器里的进程                   |                           |
| docker exec -it 容器id /bin/bash                         | 进入容器                           |                           |
| exit                                                     | 退出容器                           |                           |
| sudo docker ps -a -q                                     | 查看所有容器ID                     |                           |
| sudo docker stop $(sudo docker ps -a -q)                 | stop停止所有容器                   |                           |
| sudo docker  rm $(sudo docker ps -a -q)                  | remove删除所有容器                 |                           |
| docker stop $(docker ps -q) & docker rm $(docker ps -aq) | 一次性停止删除容器                 |                           |

### 三、容器进程

格式：docker top [OPTIONS] CONTAINER [ps OPTIONS]
* 列出redis容器中运行进程
docker top redis
* 查看所有运行容器的进程信息
for i in `docker ps |grep Up|awk '{print $1}'`;do echo \ &&docker top $i; done

### 四、容器日志

* 查看redis容器日志，默认参数
docker logs rabbitmq
* 查看redis容器日志，参数：-f 跟踪日志输出；-t  显示时间戳；--tail 仅列出最新N条容器日志；
docker logs -f -t --tail=20 redis
* 查看容器redis从2019年05月21日后的最新10条日志。
docker logs --since="2019-05-21" --tail=10 redis

### 五、容器与主机间的数据拷贝

* 将rabbitmq容器中的文件copy至本地路径
 docker cp rabbitmq:/[container_path] [local_path]
* 将主机文件copy至rabbitmq容器
 docker cp [local_path] rabbitmq:/[container_path]/
* 将主机文件copy至rabbitmq容器，目录重命名为[container_path]（注意与非重命名copy的区别）
 docker cp [local_path] rabbitmq:/[container_path]