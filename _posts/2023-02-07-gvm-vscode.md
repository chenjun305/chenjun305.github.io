---
layout: post
title: golang多版本管理工具gvm
date: 2023-02-07 17:00:00
description: 
tags: golang
categories: programming
---
### gvm简介
项目地址： [https://github.com/moovweb/gvm](https://github.com/moovweb/gvm)

gvm会将不同的golang版本安装在不同的目录下，每个版本的go path目录也不同，从而达到隔离的目的。  
通过切换goroot, gopath环境变量来达到切换版本的目的。

### 安装gvm
Mac OS X下需要先安装依赖：  
```
$ xcode-select --install
$ brew update
$ brew install mercurial
```
然后安装gvm工具：
```
$ bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
$ source /Users/yueqi/.gvm/scripts/gvm
```
gvm工具安装在当前用户的.gvm目录下。

### 安装golang
```
// 列出所有已安装的go版本
$ gvm list
// 查看可以下载的golang版本
$ gvm listall
```
Go 1.5+移除了C Compilers，而使用golang重新实现了。所以要编译1.5+版本，需要一个可工作的go版本。   
另外，编译Go 1.20+ 需要 go1.17.3+
```
$ gvm install go1.4 -B 
$ gvm use go1.4
$ gvm install go1.17.13
$ gvm use go1.17.13
$ gvm install go1.20.7
$ gvm use go1.20.7
```

### VS Code配置
VS Code默认是去找安装在/usr/local/bin下的golang, 需要配置VS Code去找GVM安装的golang.  
通过配置settings.json可以实现。  
mac下该文件位于 $HOME/Library/Application Support/Code/User/settings.json  

打开VS Code后，按快捷键 Command + P 然后输入 > Open User Settings(JSON),  
然后增加如下配置：  
```
"go.goroot": "/Users/yueqi/.gvm/gos/go1.20.7",
"go.gopath": "/Users/yueqi/.gvm/pkgsets/go1.20.7/global",
```
