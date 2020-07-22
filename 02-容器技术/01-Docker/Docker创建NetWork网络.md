

[TOC]



### 一、创建bridge网络相关命令

##### 1、不指定网络驱动时默认创建的bridge网络
docker network create default_network

##### 2、查看网络内部信息
docker network inspect default_network

##### 3、列所有列表的网络
docker network ls

##### 4、移除指定的网络
docker network rm default_network