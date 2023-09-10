---
layout: post
title: 分布式事务 - TCC
date: 2023-03-03
description: 
tags: microservices
categories: programming
---
### TCC简介
关于 TCC（Try-Confirm-Cancel）的概念，最早是由 Pat Helland 于 2007 年发表的一篇名为[《Life beyond Distributed Transactions:an Apostate’s Opinion》](http://adrianmarriott.net/logosroot/papers/LifeBeyondTxns.pdf)的论文提出。

TCC分为3个阶段

* Try 阶段：尝试执行，完成所有业务检查, 预留必须业务资源;
* Confirm 阶段：确认执行真正业务，不作任何业务检查，只使用 Try 阶段预留的业务资源，Confirm 操作要求具备幂等设计，Confirm 失败后需要进行重试;
* Cancel 阶段：取消执行，释放 Try 阶段预留的业务资源。Cancel 阶段的异常和 Confirm 阶段异常处理方案基本上一致，要求满足幂等设计。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/TCC1.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

以转账作为例子的话，通常会在Try里面冻结金额，但不扣款，Confirm里面扣款，Cancel里面解冻金额

TCC的Confirm/Cancel阶段在业务逻辑上是不允许返回失败的，如果因为网络或者其他临时故障，导致不能返回成功，需要不断地重试，直到Confirm/Cancel返回成功。

TCC特点如下：
* 并发度较高，无长期资源锁定。
* 开发量较大，需要提供Try/Confirm/Cancel接口。
* 相对于Saga，用户看不到中间态，适用于对中间状态有约束的业务


### TCC设计原则
在设计上，TCC主要用于处理一致性要求较高、需要较多灵活性的短事务。  
例如，A跨行转账给B的场景，如果采用SAGA，在正向操作中调余额，在补偿操作中，反向调整余额，那么会出现这种情况：如果A扣款成功，金额转入B失败，最后回滚，把A的余额调整为初始值。整个过程中如果A发现自己的余额被扣减了，但是收款方B迟迟没有收到资金，那么会对A造成非常大的困扰。

上述需求在SAGA中无法解决，但是可以通过TCC来解决，设计技巧如下：

* 在账户中的 balance 字段之外，再引入一个 trading_balance 字段
* Try 阶段检查账户是否被冻结，检查账户余额是否充足，没问题后，调整 trading_balance （即业务上的冻结资金）
* Confirm 阶段，调整 balance ，调整 trading_balance （即业务上的解冻资金）
* Cancel 阶段，调整 trading_balance （即业务上的解冻资金）

这种情况下，终端用户 A 就不会看到自己的余额扣减了，但是 B 又迟迟收不到资金的情况

### TCC的注意事项
* 业务操作分两阶段完成;  
接入TCC前，业务操作只需要一步就能完成，但是在接入TCC之后，需要考虑如何将其分成2阶段完成，把资源的检查和预留放在一阶段的Try操作中进行，把真正的业务操作的执行放在二阶段的Confirm操作中进行；  
TCC服务要保证第一阶段Try操作成功之后，二阶段Confirm操作一定能成功；
* 允许空回滚；  
事务协调器在调用TCC服务的一阶段Try操作时，可能会出现因为丢包而导致的网络超时，此时事务协调器会触发二阶段回滚，调用TCC服务的Cancel操作；  
TCC服务在未收到Try请求的情况下收到Cancel请求，这种场景被称为空回滚；TCC服务在实现时应当允许空回滚的执行；  
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/TCC2.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
* 防悬挂控制；  
如下图所示，事务协调器在调用TCC服务的一阶段Try操作时，可能会出现因网络拥堵而导致的超时，此时事务协调器会触发二阶段回滚，调用TCC服务的Cancel操作；在此之后，拥堵在网络上的一阶段Try数据包被TCC服务收到，出现了二阶段Cancel请求比一阶段Try请求先执行的情况；  
用户在实现TCC服务时，应当允许空回滚，但是要拒绝执行空回滚之后到来的一阶段Try请求；  
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/TCC3.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
* 幂等控制：  
无论是网络数据包重传，还是异常事务的补偿执行，都会导致TCC服务的Try、Confirm或者Cancel操作被重复执行；用户在实现TCC服务时，需要考虑幂等控制，即Try、Confirm、Cancel 执行一次和执行多次的业务结果是一样的；  
* 业务数据可见性控制；  
TCC服务的一阶段Try操作会做资源的预留，在二阶段操作执行之前，如果其他事务需要读取被预留的资源数据，那么处于中间状态的业务数据该如何向用户展示，需要业务在实现时考虑清楚；通常的设计原则是“宁可不展示、少展示，也不多展示、错展示”；  
* 业务数据并发访问控制；  
TCC服务的一阶段Try操作预留资源之后，在二阶段操作执行之前，预留的资源都不会被释放；如果此时其他分布式事务修改这些业务资源，会出现分布式事务的并发问题；  
用户在实现TCC服务时，需要考虑业务数据的并发控制，尽量将逻辑锁粒度降到最低，以最大限度的提高分布式事务的并发性；  
