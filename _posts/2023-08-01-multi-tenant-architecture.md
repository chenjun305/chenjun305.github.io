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

