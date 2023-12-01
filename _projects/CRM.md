---
layout: page
title: CRM
description: Field Operation Management System for O2O service
img: assets/img/CRM.png
importance: 1
category: work
---
## architecture

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/crm-architecture.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
  CRM architecture
</div>

## 微服务架构
### 核心业务层
* leads 商业线索，商机
* merchant 商户系统
* checkin 现场拜访服务，销售市场人员拜访客户
* tickets 工单系统
* marketting 营销系统
* AreaManagement 区域管理

### 业务支撑层
* account 账户服务，管理市场推广，销售人员用户，权限，组织架构等
* notification 通知jj
* File 文件服务
* POI 地理位置服务
* performance 绩效系统
* ...

## tech stack
* programming language: golang, python
* architecture: microservices 
* Api: rest/json, grpc/protobuf
* api gateway: using gin to implement the api gateway
* DBMS: mysql
* Search: ElasticSearch
* Cache: redis
* Storage: Ceph
* observability：prometheus, grafana, OpenTelemetry, JAEGER, Sentry
* deploy on kubernetes using jenkins/ansible

### 分布式事务
服务间写请求通过基于消息的异步通信机制实现。因为直接通过接口调用的方式无法保证分布式事务的一致性。
因为网络是不可靠的，可能会丢包，进程也可能突然宕机。

只有读请求才可以通过同步的API调用实现。读请求不会修改数据，不会造成分布式数据的不一致。

### 查询分离模式
1. Mysql的主从分离，肯定是有，技术比较成熟，不用多说。

2. 通过ElasticSearch支持全文检索和地理位置搜索
但ElasticSearch的数据是通过Mysql的binlog异步同步而来。可以使用canal, go-mysql-elasticsearch等实现。

### 多国家部署方案
* 1套代码1套数据库设计，支持多个国家的需求差异。
* 代码目录层次，代码实现方式进行规范，对于差异化的需求，通过定义接口，不同国家提供不同的实现方式来实现。
* 通过配置开关来控制功能是否开启。

### 微服务架构演进
* 采用小步快跑的方式，结合业务需求的规划，逐步拆分服务。如 业务需求多，需求变化频繁的服务先进行重构。
* 采用分布进行，先读接口，后写接口，先重构代码，再拆分数据库的形式。
* 确保平滑上线，如先双读，双写的方式，确保新服务没有问题后，再下线旧服务。
* 把重构的技术需求 和 业务重构的需求 一起实现，节约研发测试资源。


### 技术架构的演进
* 文件存储迁移到Ceph
  * 迁移步骤： 双写读旧，双写读新，单写读新
* 缓存层从memcache迁移到redis 
* 引入Kafka, 通过异步消息的方式解决分布式事务一致性的问题。
* 通过binlog的方式实现DB数据和ES数据的增量同步。
* 项目工程管理的优化，从不同服务不同的repo到所有服务放到1个repo中。简化代码共享、版本控制、构建和部署等方面的复杂性，并提供更好的可重用性和协作性。
* devops 单元测试，静态代码扫描
* BFF层，主要针对跨服务查询，聚合不同微服务的数据，减少客户端特别是App端的接口请求次数。通过GraphQL接口来实现。

### 可观测性建设
* Prometheus + Grafana 技术指标，业务指标监控，异常告警
* OpenTelemetry + Jaeger 调用链
* Log 日志平台
* Sentry 错误监控




