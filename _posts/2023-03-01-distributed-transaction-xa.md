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

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/xa.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    DTP Model, XA规范就是约定DTP模型中的两个模块事务管理器和资源管理器的通讯方式
</div>

### MySQL的XA
在分布式事务中，MySQL的角色其实是XA事务过程中的RM。在分布式事务中一般会涉及到至少两个RM，我们说的MySQL支持XA协议就是说MySQL实现了XA协议中RM应该具有的功能。
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
XA事务状态进展过程  

1. 使用XA START 启动一个XA事务，并把它置为ACTIVE状态。
2. 对一个ACTIVE XA事务，发布构成事务的SQL语句，然后发布一个XA END 语句，XA END 把事务置为IDLE状态。
3. 对一个IDLE XA 事务， 发布一个XA PREPARE语句或者一个XA COMMIT ... ONE PHASE语句： 前者把事务置为PREPARE状态,后者用于预备和提交事务。
4. 对一个PREPARE XA 事务，可以发布一个XA COMMIT语句来提交和终止事务，或者发布一个XA ROLLBACK 来回滚并终止事务。  

在用一个客户端环境下，XA事务和本地(非XA)事务互相排斥，如果已经发布了XA START来开启一个事务，则本地事务不会被启动，知道XA事务被提交或者被回滚为止；相反的，如果已经使用START TRANSACTION启动一个本地事务，则XA语句不能被使用，直到该事务被提交或者回滚为止.  

### XA的优点
* 强一致性
* 简单易理解，可以让程序员可以采用跟本地事务几乎一样的编程模型来解决分布式场景下的问题。

### XA的缺点
* 许多新技术，包括NOSQL数据库（例如MongoDB和Cassandra），并不支持XA标准的分布式事务。
* 一些流行的消息代理如RabbitMQ和Apache Kafka也不支持分布式事务。
* 为了让一个分布式事务提交，所有参与事务的服务都必须可用，系统整体的可用性是所有事务参与方可用性的乘积。
* 对资源进行了长时间的锁定，并发度低，不适合高并发的业务

### 参考资料
* [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA)
* [MySQL XA Transactions](https://dev.mysql.com/doc/refman/8.0/en/xa.html)
* [Distributed Transaction Processing: The XA Specification](https://pubs.opengroup.org/onlinepubs/009680699/toc.pdf)


