---
layout: post
title: Salesforce元数据驱动的多租户架构
date: 2023-08-01 17:00:00
description: 
tags: architecture
categories: programming
---
近段时间，我主要在从事CRM系统的架构工作，自然对业界相关的技术进行了一些调研，Salesforce是CRM领域的标杆和鼻祖，也是全球企业信息服务界的龙头。本文记录我调研整理的Salesforce的后端技术架构相关信息。

### Salesforce简介
#### Salesforce发展史
* 1999年，由前甲骨文高管Marc Benioff创立
* 2001年，推出了第一款SaaS应用CRM，受到热议
* 2004年，纽交所上市，市值10亿美元，股票代码CRM
* 2005年，推出了名为“AppExchange”的程序商店
* 2007年，推出了PaaS平台Force.com，让用户更方便地开发在线应用
* 2009年，推出了”Service Cloud”在线客户服务应用
* 2012年，推出“Marketing Cloud”
* 2015年，构建电商云
* 2016年，推出了AI产品Einstein，将AI技术引入到SaaS服务中
* 2019年，收购数据分析平台Tableau
* 2021年，收购企业通讯SaaS软件Slack，补强协同办公功能

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-history.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce发展史中的重要节点
</div>

#### 全球CRM市场份额

Salesforce近年来在 CRM 行业的市场份额稳居第一，2021 年市占率为 23.8%，SAP 和 Microsoft 位列 第二和第三，市占率均不及 5.5%，与 Salesforce 的差距较大
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/crm-share.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    全球CRM市场份额
</div>

#### Salesforce付费模式
Salesforce采用的是订阅付费模式，用户的续费率至关重要。如果Salesforce的产品不能为企业创造真正的价值，那么第二期的续费将得不到保证。
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/sales-cloud-pricing.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce订阅付费模式
</div>

同时其订阅费用采用阶梯定价的收费模式，产品能力是吸引客户提升付费等级的关键因素。
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/sales-cloud-pricing2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce的阶梯定价收费模式
</div>

#### Salesforce围绕CRM的4朵云
* 销售云（sales cloud)
  * 服务于销售部门，销售流程的规范化和提升销售效率。销售线索、潜在客户的获取， 销售流程的自动化推进，销售过程跟踪和共享。
* 服务云（service cloud）
  * 服务于客服部门，高效互动，提升客服效率。Salesforce呼叫中心提供多渠道、全时段的客户服务支持，客户行为记录以提供个性化服务。
* 营销云（marketing cloud）
  * 服务于市场部门，营销策略的管理和销售数据传递。用户画像、定制化的营销方式、社交媒体营销、数字化营销、跨渠道营销。
* 商业云（commerce cloud）
  * 提供商业变现的渠道。企业在线上进行直接的销售，提供交易撮合、订单管理等商业服务，提升消费体验。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-income.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce公司收入结构
</div>

#### Salesforce产品矩阵

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-product.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce产品矩阵
</div>

### 技术架构
企业运维架构经历了从On-Premises(自建机房) -> IaaS(基础设施即服务) -> PaaS（平台即服务） -> SaaS（软件即服务）的演进，越往后，企业所要关注的点越少。
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/platform-architecture1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    企业运维架构的演进
</div>


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/platform-architecture2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    主要代表平台
</div>

Salesforce是SaaS和PaaS思想的首推者。  
构成其平台能力的核心主要是其元数据驱动的多租户架构。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-architecture1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce的技术架构
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-core-architecture.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce技术架构的核心
</div>

### 多租户架构
提到多租户，我们可以首先可以联想到现实生活中的多租户的概念。  
比如我们公司租了一栋大楼里的一间房，我们和这栋大楼的其他租户是共享这栋大楼的基础设施，比如电力供应，自来水，电梯，物业安保等，但我们租的那间房只有我们公司的员工能进入和使用，其他租户的员工无权进入。我们要缴纳租金，我们使用的水电费等。
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/building-tenant.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    现实生活中大楼里的租户
</div>

同样的，软件世界里的多租户，也是类似的概念，同一个SaaS平台的租户共享硬件设备和存储等基础设施。同一个租户内的不同用户间共享数据，不同的租户间通过软件进行隔离。
还有需要注意的是，这里的多租户架构和传统的软件销售On-Premises方式，软件供应商给每个客户安装一套系统不同，这里的多租户架构是所有客户共享一套软件基础设施。

* 概念
  * 多租户指得就是一个单独的软件实例可以为多个组织服务。
  * 设计上对数据和配置信息进行虚拟分区，需要对数据库结构进行特殊的设计。
  * 安全和隔离性要有所保障。
* 多租户和多用户的区别
  * 多用户的关键点在于不同的用户拥有不同的访问权限，但多个用户共享相同的业务数据；
  * 多租户间的业务数据是隔离的。
* 多租户和虚拟化的区别
  * 作用的层次不同，虚拟化是虚拟出一个操作系统，多租户是虚拟出一个应用实例。

#### 多租户架构的优缺点
* 优点
  * 经济
  * 易于更新和开发
  * 管理方便
  * One version, Bugs fixed for everyone

* 缺点
  * 更复杂
  * 不够安全/租户间隔离性

#### 多租户架构的几种模型
区别主要在于采用不同的数据库模式（Database Schema)

* 私有表
  * 机制：为每个租户的自定义数据创建一个新表
  * 优点：简单
  * 缺点：需要DDL操作，低整合度
* 扩展表
  * 机制：一个扩展表会被多个租户共享
  * 优点：高整合度，少DDL操作
  * 缺点：有点复杂
* 通用表
  * 机制：通过一个通用表来存放所有自定义信息
  * 优点：极高整合度，无DDL操作
  * 缺点：实现难度高

#### Salesforce的多租户架构
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-multi-tenant.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce的多租户架构
</div>

### 元数据驱动(Metadata Driven)
#### 什么是元数据？
* 业务数据
  * 指的是客户，联系人，业务机会，合同等跟业务发生关联的数据。
* 元数据
  * 关于数据的数据
  * 指的是对象，字段，页面布局，验证规则，工作流等这类定义应用本身的数据。
  * 也被称为UDD （Universal Data Dictionary）通用数据字典
  
#### 元数据驱动的实现原理
理论上来说，我们只需要3张表就可以实现所有租户的所有需求。  
不同租户通过唯一的OrgID隔离，所有数据访问操作都要带OrgID参数，value0~value500存储对象的不同属性。  


