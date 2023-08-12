---
layout: post
title: 分布式事务 - XA
date: 2023-03-01 17:00:00
description: 
tags: microservices
categories: programming
---
### XA简介
在多个服务，数据库之间维持数据一致性的传统方式是分布式事务。  
分布式事务的事实标准是X/Open组织提出的XA(short for "eXtended Architecture")规范。  
XA采用了两阶段提交(two phase commit)来保证事务中所有的参与方同时完成提交，或者在失败时同时回滚。
目前主流的数据库基本都支持XA事务，包括mysql、oracle、sqlserver、postgre

### MySQL的XA
MySQL要支持XA事务需要满足以下几个条件：
* XA事务仅仅被InnoDB存储引擎支持
* 确认是否开启了XA 功能
```
$ show variables like 'innodb_support_xa';
$ set innodb_support_XA = on;
```
* 只有当隔离级别设置为Serializable的时候才能使用分布式事务
```
$ show variables like 'tx_isolation';
$ set global tx_isolation='serializable', session tx_isolation='serializable';
```


mysql支持的XA事务相关SQL语法：
```
XA {START|BEGIN} xid [JOIN|RESUME]  //标记一个事务分支的开始

XA END xid [SUSPEND [FOR MIGRATE]] //标记一个事务分支的结束，Start和End之间的SQL语句就是整个事务需要进行的操作

XA PREPARE xid

XA COMMIT xid [ONE PHASE]

XA ROLLBACK xid

XA RECOVER [CONVERT XID] //查看处于PREPARE 阶段的所有XA事务
```

以下是一个例子：
```
mysql> XA START 'xatest';
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO mytable (i) VALUES(10);
Query OK, 1 row affected (0.04 sec)

mysql> XA END 'xatest';
Query OK, 0 rows affected (0.00 sec)

mysql> XA PREPARE 'xatest';
Query OK, 0 rows affected (0.00 sec)

mysql> XA COMMIT 'xatest';
Query OK, 0 rows affected (0.00 sec)
```


### XA的优点
* 强一致性
* 简单易理解，可以让程序员可以采用跟本地事务几乎一样的编程模型来解决分布式场景下的问题。


### XA的缺点
* 许多新技术，包括NOSQL数据库（例如MongoDB和Cassandra），并不支持XA标准的分布式事务。
* 一些流行的消息代理如RabbitMQ和Apache Kafka也不支持分布式事务。
* 为了让一个分布式事务提交，所有参与事务的服务都必须可用，系统整体的可用性是所有事务参与方可用性的乘积。
* 对资源进行了长时间的锁定，并发度低，不适合高并发的业务


