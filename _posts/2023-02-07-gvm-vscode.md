---
layout: post
title: golang多版本管理工具gvm
date: 2023-02-07 17:00:00
description: 
tags: golang
categories: programming
---
### gvm简介
* 项目地址： https://github.com/moovweb/gvm 

gvm会将不同的golang版本安装在不同的目录下，每个版本的go path目录也不同，从而达到隔离的目的。
通过切换goroot, gopath环境变量来达到切换版本的目的。

### 安装gvm
```
$ bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
$ source /Users/yueqi/.gvm/scripts/gvm
```
gvm工具安装在当前用户的.gvm目录下。

### 安装golang
编译Go 1.20+ 需要 go1.17.3+
```
$ gvm install go1.17.13 -B
$ gvm use go1.17.13
$ gvm install go1.20.7
$ gvm use go1.20.7
```

### VS Code配置
VS Code默认是去找安装在/usr/local/bin下的golang, 需要配置VS Code去找GVM安装的golang.
通过配置settings.json可以实现。
mac下该文件位于 $HOME/Library/Application Support/Code/User/settings.json 

打开VS Code后，按快捷键<command> + P 然后输入 > Open User Settings(JSON),
然后增加如下配置：
```
"go.goroot": "/Users/yueqi/.gvm/gos/go1.20.7",
"go.gopath": "/Users/yueqi/.gvm/pkgsets/go1.20.7/global",
```
