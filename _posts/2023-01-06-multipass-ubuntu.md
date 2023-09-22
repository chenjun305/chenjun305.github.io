---
layout: post
title: multipass管理ubuntu虚拟机
date: 2023-01-06
description: 
tags: Linux
categories: programming
---

## 简介
最近在浏览[Ubuntu官网](https://cn.ubuntu.com/)的时候，发现了一款叫[Multipass](https://multipass.run/)的东西。

它可以快速在电脑上搭建一个轻量级的虚拟机，并且相比于 Vmware 或 [VirtualBox](https://www.virtualbox.org/) 更加轻量，只需一行命令快速创建 Ubuntu 虚拟机。

Multipass 是一个轻量虚拟机管理器，是由 Ubuntu 运营公司 Canonical 所推出的开源项目。运行环境支持 Linux、Windows、macOS。在不同的操作系统上，使用的是不同的虚拟化技术。在 Linux 上使用的是 KVM、Window 上使用 Hyper-V、macOS 中使用 HyperKit 以最小开销运行VM，支持在笔记本模拟小型云。

同时，Multipass 提供了一个命令行界面来启动和管理 Linux 实例。下载一个全新的镜像需要几秒钟的时间，并且在几分钟内就可以启动并运行 VM。（直呼好家伙，大大降低的我们安装虚拟机的成本了）

## 安装
MacOS下可直接使用brew进行安装。

```
brew install --cask multipass
```

这是我见过的史上最快的创建并启动一个linux虚拟机的方法，安装完成后，启动multipass, 将在顶部显示multipass的图标，点击 图标 -> open shell

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/multipass-open-shell.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    </div>
</div>

等待一会，multipass就帮你下载安装好了一个虚拟机，并进入了该虚拟机的shell界面。
背后，multipass做了如下事情：
  * 创建了一个叫做`primary`的虚拟机, 默认配置是：1GB内存, 5GB磁盘, 1 CPU;
  * 安装最新的Ubuntu LTS release;
  * mount宿主机`$HOME`目录到虚拟机内的`~/HOME/`目录;
  * 进入虚拟机的shell, 你将看到命令提示符：`ubuntu@primary:~$`

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/multipass-shell.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

上面的操作，相当于在宿主机执行如下命令：
```
$ multipass launch –name primary 
$ multipass shell
```
`multipass shell`命令后不跟虚拟机名字的话，默认使用`primary`这个名字。

## 使用

* 查看可供下载的 Ubuntu 镜像

```
$ multipass find
```

* 创建并启动一个虚拟机(使用lts镜像)

```
$ multipass launch lts --name ltsInstance --memory 2G --disk 10G --cpus 2
```


* 列出所有虚拟机实例

```
$ multipass list
```

* 查看虚拟机信息（IP，资源使用情况等）

```
$ multipass info ltsInstance
```

* 不进入虚拟机执行命令

```
$ multipass exec ltsInstance -- lsb_release -a
$ multipass exec ltsInstance -- ls
```

* 进入虚拟机

```
$ multipass shell ltsInstance
```
此时便进入到了Ubuntu 环境中, 在里面可以执行相关的 linux 指令, 例如，给系统设置一个 root 密码，设置好密码后，使用 su root 切换到 root 用户。
```
# 设置密码
sudo passwd
# 切换 root
su root
```
ubuntu 是使用 apt-get 来进行包管理的，首先更新一下 apt-get，然后安装 nginx
```
# 更新apt
apt-get update
# 安装 nginx
apt-get install nginx
# 安装好 nginx 后，可以到 /etc/nginx 目录即可看到刚刚的 nginx 配置信息
cd /etc/nginx/
```

* 挂载数据卷

```
# 挂载格式
multipass mount 宿主机目录  实例名:虚拟机目录
#卸载数据卷
multipass umount 实例名[:<path>]
```

* 传输文件

```
multipass transfer 宿主机文件 实例名:虚拟机目录
```

* 删除和释放实例

```
# 启动实例
multipass start ltsInstance
# 停止实例
multipass stop ltsInstance

# 删除实例（删除后，还会存在）
$ multipass delete ltsInstance 
# 恢复删除的实例
$ multipass recover ltsInstance 
# 释放实例（彻底删除）
$ multipass purge ltsInstance 
```

* 卸载multipass

```
$ brew uninstall multipass
# or
$ brew uninstall --zap multipass # to destroy all data, too
```

## multipass + cloud-init
[Cloud-init](https://cloud-init.io/) is the standard for customizing cloud instances,

```
$ multipass launch -n my-test-vm --cloud-init cloud-config.yaml
```

上面 config.yaml 则是容器的初始化配置文件, 通过创建虚拟机时提供一个配置文件，为了保持开发环境和线上环境一致性 同时节省部署时间.

例如，我们想在初始化容器的时候，自动下载安装 Node.js，内容如下：
```
#cloud-config
runcmd:
  - curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
  - sudo apt-get install -y nodejs
```
凡是用户自定义的cloud-init的配置文件,必须以#cloud-config开头，这是cloud-init识别它的方式。
runcmd 可以指定容器 首次启动 时运行的命令

更多的配置方法可参考[cloud-init文档](https://cloudinit.readthedocs.io/en/latest/reference/examples.html)

