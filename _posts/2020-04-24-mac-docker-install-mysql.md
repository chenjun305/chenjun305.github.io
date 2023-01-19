---
layout: post
title: Mac通过Docker Desktop安装MySQL
date: 2020-04-24 13:56:49
description: 
tags: docker
categories: devops
---

### 下载 Docker Desktop

[https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)

### 创建存放Mysql数据的目录

```
$ mkdir -p /Users/junchen/Develop/mysql_data/8.0
```

### 创建网络

使相同网络的容器间可以通过name通信（而不是IP）。
```
$ docker network create dev-network
```

### 创建MySQL8.0容器

```
$ docker run --restart always --name mysql8.0 --net dev-network -v /Users/[your_username]/Develop/mysql_data/8.0:/var/lib/mysql -p 3306:3306 -d -e MYSQL_ROOT_PASSWORD=[your_password] mysql:8.0
```
注意替换[your_username] ， [your_password] 为你自己的。
此命令拉取官方docker hub的image，可能需要连外网。

### 安装并安装Mysql workbench（8.0版）

[https://dev.mysql.com/downloads/workbench/](https://dev.mysql.com/downloads/workbench/)

### 连接docker中的Mysql

创建数据库创建表都没有问题，并且数据不会丢失。

### 进入容器命令行

```
$ docker exec -it bddb2e3371b8 bash
```