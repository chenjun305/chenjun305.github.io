---
layout: post
title: docker使用
date: 2023-04-05
description: 
tags: docker
categories: programming
---

## [docker command](https://docs.docker.com/engine/reference/commandline/cli/)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/docker-command.webp" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
  docker command
</div>


### 构建镜像 
* [Dockerfile](https://docs.docker.com/engine/reference/builder/)

Dockerfile的设计思想是使用一些标准原语描述所要构建的Docker镜像，并且这些原语都是按顺序处理的。  
以下是一个构建python应用的例子：

```
#使用官方提供的Python开发镜像作为基础镜像
FROM python:2.7-slim

#将工作目录切换为 /app
WORKDIR /app

#将当前目录下的所有内容复制到 /app下
ADD . /app

#使用pip命令安装这个应用所需要的依赖
#RUN原语就是在容器里执行shell命令
RUN pip install --trusted-host pypi.python.org -r requirements.txt

#允许外界访问容器的80端口
EXPOSE 80

#设置环境变量
ENV NAME World

#设置容器进程为: python app.py
CMD ["python","app.py"]   
```

另外在使用Dockerfile时，你可能还会看到叫ENTRYPOINT的原语, 实际上它和CMD都是Docker容器进程启动所必须的参数.  
完整执行格式是: `ENTRYPOINT CMD`  
在默认情况下Docker会提供一个隐含的ENTRYPOINT，即: `/bin/sh -c`  
所以在这个例子里，实际运行在容器里的完整进程是: `/bin/sh -c "python app.py"`

* `docker build`

```
docker bulid -t helloworld . 
```

docker build会自动加载当前目录下的Dockerfile文件，然后按照顺序执行文件中的原语。
-t的作用是给这个镜像加一个Tag，即起名字
Dockerfile中的每个原语执行后，都会生成一个对应的镜像层，即使原语本身并没有明显修改文件的操作（如ENV原语），它对应的层也会存在，只不过在外界看来是空的

* `docker commit`

除了`docker build`命令，还可以使用`docker commit`指令，把一个正在运行的容器，直接提交一个镜像。`docker commit`实际上是容器运行起来后，把最上层的可读写层，加上原先容器镜像的只读层（在宿主机上共享，不会占用额外的空间），打包组成了一个新的镜像。  

* 查看镜像(`docker image`)

```
$ docker image ls
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
helloworld                           latest              482458a88f79        2 hours ago         131MB
```
### 启动容器 
* `docker run`

```
docker run -p 4000:80 helloworld
```
因为在Dockerfile中已经制定了CMD，否则就的把进程启动命令加载后面：
```
docker run -p 4000:80 helloworld python app.py
```
`-p 4000:80`是把容器内的80端口映射在宿主机的4000端口上。
这样做得目的是只要访问宿主机的4000端口就可以看到容器里应用返回的结果，
否则就要先用`docker inspect`命令查看容器的IP地址，然后访问“http://<容器IP地址 >：80”才可以看到容器内应用的返回.

* 查看容器（`docker ps`)

### [Docker Hub](https://hub.docker.com/)
如果需要上传到Docker Hub, 则需要先注册账号，然后通过`docker login`命令登录，然后用`docker tag`给容器起一个完整的名字.最后通过`docker push`命令上传到docker hub.

我们企业内部内部能否也搭建一个跟Docker Hub类似的镜像上传系统呢？  
当然可以，这个统一存放镜像的系统就叫做[Docker Registry](https://docs.docker.com/registry/spec/api/), 也可以查看VMware的[Harbor](https://goharbor.io/)项目。


## [docker compose](https://docs.docker.com/compose/reference/)
### [compose.yaml](https://docs.docker.com/compose/compose-file/)
定义多容器应用。

```
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
```

the web service uses an image that's built from the Dockerfile in the current directory.

* `docker compose up` 或 `docker compose up -d` Create and start containers
* 查看 `docker compose ps` List containers
* 停止 `docker compose stop` Stop services
* `docker compose down --volumes` Stop and remove containers, networks, also remove the data volume 
