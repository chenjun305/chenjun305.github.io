---
layout: post
title: Salesforce元数据驱动的多租户架构
date: 2023-08-01
description: 
tags: architecture
categories: programming
---
近段时间，我主要在从事CRM系统的架构工作，自然对业界相关的技术进行了一些调研，Salesforce是CRM领域的标杆和鼻祖，也是全球企业信息服务界的龙头。本文记录我调研整理的Salesforce的后端技术架构相关信息。

### Salesforce简介
#### Salesforce发展史
Salesforce的创立者叫Marc Benioff。1999年时，他27岁，为当时美国一家软件巨头Oracle最年轻的高级副总裁。20世纪90年代中后期，像Oracle这样的企业售卖软件按Licence（对付费用户进行授权，以正常使用软件的完整功能）收费，并由软件厂商去部署安装。Marc任Oracle高级副总裁期间，形成了将CRM等通用软件通过互联网部署的想法：用云端模式交付，使用者则像“交水电费那样为自己的软件付费。但他的想法并没有得到前东家的认可，于是决定创业，1999年3月，Salesforce诞生。

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

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/metadata1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    元数据驱动的实现原理
</div>

#### Salesforce数据层
* 元数据表（Metadata Tables）
* 数据表（Data Tables）
* 索引表（Specialized Pivot tables）

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-data-layer.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce数据层
</div>

##### Data表
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-data-table.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Data表
</div>
大多数结构化数据都以可变字符串形式存储在value0~500的列里。（包括数字，日期等），但无法利用底层数据库索引的能力快速查询和排序？怎么解决呢？ 

##### indexes表
解决方案是把数据拷贝出数据表并转换成原始的的数据类型，并存储到Indexes索引表列内，  
Indexes 表的底层索引是标准的，采用非唯一性的数据库索引。  
查询先查indexes表，再查data表获取数据。  
indexes表的字段定义如下：
* OrgID：其所归属的应用对象所归属的租户OrgID
* ObjID：字段所属应用对象唯一标识
* FieldNum：对象字段存储位置
* ObjInstanceGUID：对象实例唯一标识
* StringValue：强类型的字符串列
* NumValue：强类型的数字列
* DateValue：强类型的日期列

##### Unique Indexes表
这个表非常类似于 Indexes 表，不过 Unique indexes采用底层原生的数据库索引来强制唯一性校验。
* UniqueStringValue：唯一的字符串列
* UniqueNumValue：唯一的数字列
* UniqueDateValue：唯一的日期列
* 其他字段定义请参考 Indexes 透视表

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-indexes-table.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-unique-indexes-table.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    indexes表和unique indexes表
</div>

##### Relationship索引表
* 字段
  * OrgID:租户ID
  * ObjID:子对象的对象标识
  * GUID：子对象实例的唯一表识
  * RelationID：子对象内关系字段定义的标识
  * TargetObjInstanceID：父对象实例的唯一标识
* 定义了两个底层数据库复合索引存储引用关系
  * 第一个索引字段：OrgID + GUID，用于从子对象到父对象的关联查询。
  * 第二个索引字段：OrgID + ObjID + RelationID + TargetObjInstanceID，用于父对象到子对象的关联查询。

  ##### 如何支撑多租户巨大数据量？
* Data Partitioning数据分区
  * 所有的 Force.com 的数据，元数据，透视表结构，包含底层数据库索引，都是通过对 OrgID 进行物理分区的，采用的是原生的数据库分区机制。
  * 数据分区是数据库系统提供的被验证过的技术，用以物理划分较大的逻辑数据结构到较小的可以管理的区块中。

##### 无感的对象结构变更No DDL
* 在元数据驱动的数据架构中，所有的 DDL 语言操作对应的使元数据层的元数据的记录的更新，不涉及数据库物理结构的更新
* 当用户修改了一个表字段列的数据结构，从一种数据类型改成另外一种不同存储格式的数据类型时候，系统会重新分派一个新的弹性列给到这个字段列的数据，将数据从原来的存储弹性列批量拷贝到新的弹性列，然后才会更新此字段列的元数据，暨在 Fields 表中更新这个字段列的元数据，将数据类型更改为新的数据类型，并将 FieldNum 更新为新的 ValueX 列对应的X值。

### 多租户隔离和保护
* 监控每一个代码执行片段
* 限制代码CPU执行时间，内存使用量
* 限制代码DB查询和更新数量
* 限制可执行的数学运算量
* 限制外部接口调用量
* 执行太耗时或太耗资源的操作，系统将抛出运行时异常
* 单元测试覆盖率必须超过75%

因为是多租户共享，严格的限制保证整体的可扩展性和性能   
长期来看，这些限制推动了开发写出了更好的代码  

### 数据高可用
* 4 online copies
* 2 standby copies
* 本地热备切换速度更快
* 异地热备使灾难切换少于15分钟
* 每6个月切换一次（定期演练，验证）

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-data-availability.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    数据高可用
</div>

### 可视化配置
#### Salesforce能配置什么？
* 数据模型
  * 包括自定义的对象和它的相关字段，及其之间的关系。 
  * 其实就是操作元数据
* 安全和共享模型
  * 包括用户，权限等
* 用户界面
  * 包括界面布局, 数据输入表单和报表等。
* 声明式逻辑 (工作流)
* 编程式逻辑 (存储过程和触发器)

#### Salesforce工作流

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce_flow.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce工作流
</div>


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce_flow_builder.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Salesforce工作流构建器
</div>

#### 配置数据模型和用户界面
* 对象管理器
  * 其实就是创建数据模型的地方，是后台配置对象，字段，页面布局等元数据的工具
* 配置用户界面
  * 列表视图 - 列表页
    * 在前端加载对象的记录列表时使用。例如客户列表
    * 利用元数据来展示业务数据。
  * 页面布局 - (创建/更新/详情页）
    * 在前端加载一条记录详情的时候使用，
    * 例如客户详情，新增/编辑客户信息页面。

##### 可视化配置底层数据结构
橙色表示后台配置，蓝色表示前台界面  

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-data-structure.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    可视化配置底层数据结构
</div>

##### 对象管理器
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-object-manager.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
   对象管理器 
</div>

##### 对象（Object)
* 只有名称和APIName这两个重要的属性。
* 创建一个新对象的时候，系统会自动帮你创建一些系统字段、一个页面布局，以及系统标准按钮数据。
* 系统字段主要是指那些审计字段，例如：创建人、创建时间、最后更新人、最后更新时间、是否已删除等等。标准按钮主要是创建、编辑、删除等系统常规操作按钮。

##### 字段（Field）
* 每个字段都需要有个数据类型
* 一个字段只能属于1个对象
* 字段的APIName在该对象上必须唯一

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-object-field-type.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
    </div>
    <div class="col-sm mt-3 mt-md-0">
    </div>
</div>
<div class="caption">
    字段的数据类型
</div>

##### 列表视图
可灵活控制列表需要展示的字段和顺序。
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-list-view.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    列表视图
</div>

##### 页面布局(PageLayout)
* 在前端加载一条记录详情的时候使用
* 页面布局只要有2大块
  * 区域（PageLayoutSection）
  * 相关列表（RelatedList）
* 每个区域会有不同字段，可以是一列，两列，三列式
* 相关列表指的是展示子记录的列表，可配置展示子记录的哪些列

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-page-layout.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    页面布局配置页面
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-page-related-list.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    相关列表配置页面
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-detail-page.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    加载详情页面
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/salesforce-edit-page.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    加载新建和编辑页面
</div>

### 总结Salesforce的设计理念
* 数据驱动
  * 元数据驱动
* 规模经济
  * 尽可能多用户共享同一套系统
* 定制方便
* 功能丰富
  * 能让用户购买和整合第三方应用（AppExchange）
* 软件是一个进化的工程
  * 不断丰富的产品矩阵
