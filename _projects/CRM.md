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

## 微服务
* account 账户服务，管理市场推广，销售人员用户，权限，组织架构等
* leads 商业线索，商机
* merchant 商户系统
* checkin 现场拜访服务，销售市场人员拜访客户
* tickets 工单系统
* marketting 营销系统
* performance 绩效系统
* notification 通知
* ...

## tech stack
* programming language: golang, python
* architecture: grpc/protobuf microservices
* gateway: nginx & using gin to implement the api gateway
* DBMS: mysql, elasticsearch, redis
* Storage: Ceph
* deploy on kubernetes using jenkins/ansible
* observability：prometheus, grafana, OpenTelemetry, JAEGER
* refactoring from a legacy python/django project and implement so many amazing new features


