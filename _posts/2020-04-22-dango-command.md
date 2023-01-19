---
layout: post
title: django1.6常用命令总结
date: 2020-04-22 22:20:52
description: 
tags: python
categories: programming
---

### 判断django版本
```
$ python -c "import django; print(django.get_version())"
1.6.11
```
### 创建项目
```
$ django-admin.py startproject myblog
```
### 启动服务器
```
$ python manage.py runserver
$ python manage.py runserver 8080  #更改默认端口
$ python manage.py runserver 0.0.0.0:8000 #listen on all public IPs 
```
>Automatic reloading of runserver
The development server automatically reloads Python code for each request as needed. You don’t need to restart the server for code changes to take effect. However, some actions like adding files or compiling translation files don’t trigger a restart, so you’ll have to restart the server in these cases.

### 创建应用
```
$ python manage.py startapp blog
```
### 自动创建表
```
$ python manage.py sqlall polls  # 查看应用polls的SQL语句
$ python manage.py syncdb
Creating tables ...
Creating table django_admin_log
Creating table auth_permission
Creating table auth_group_permissions
Creating table auth_group
Creating table auth_user_groups
Creating table auth_user_user_permissions
Creating table auth_user
Creating table django_content_type
Creating table django_session
Creating table blog_article

You just installed Django's auth system, which means you don't have any superusers defined.
Would you like to create one now? (yes/no): yes
Username (leave blank to use 'xxx'): 
Email address: xxx@xxx
Password: 
Password (again): 
Superuser created successfully.
Installing custom SQL ...
Installing indexes ...
Installed 0 object(s) from 0 fixture(s)
```
The syncdb command looks at all your available models and creates tables in your database for whichever tables
don’t already exist.
### 进入项目的交互式Shell环境
```
$ python manage.py shell
```
### 生成项目requirements.txt
```
$ pip freeze >requirements.txt
```
### 安装python项目依赖
```
$ pip install -r requirements.txt
```