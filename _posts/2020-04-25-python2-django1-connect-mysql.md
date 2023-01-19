---
layout: post
title: python2.7+django1.6 连接MySQL8
date: 2020-04-25 18:25:31
description: 
tags: python
categories: programming
---

### 项目版本

Python 2.7.10 + Django 1.6.11 + MySQL 8.0.19
```
$ python --version
Python 2.7.10

$ python -c "import django; print(django.get_version())"
1.6.11

$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 20
Server version: 8.0.19 MySQL Community Server - GPL
```

### 使用PyMySQL模块

PyMySQL. -  [https://pypi.org/project/PyMySQL/](https://pypi.org/project/PyMySQL/)
```
$ pip install PyMySQL
......
Successfully installed PyMySQL-0.9.3
```

### 更改Django配置

在站点的__init__.py文件中必须加入一下的代码：

{% highlight python %}
import pymysql

pymysql.install_as_MySQLdb()
{% endhighlight %}

### 验证

在django项目目录中执行：
```
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
You just installed Django's auth system, which means you don't have any superusers defined.
Would you like to create one now? (yes/no): yes
Username (leave blank to use 'xxx'): admin
Email address: xxx
Password: 
Password (again): 
Superuser created successfully.
Installing custom SQL ...
Installing indexes ...
Installed 0 object(s) from 0 fixture(s)
```