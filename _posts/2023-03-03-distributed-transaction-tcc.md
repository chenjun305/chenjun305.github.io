---
layout: post
title: 分布式事务 - TCC
date: 2023-03-03 17:00:00
description: 
tags: microservices
categories: programming
---
### TCC简介
关于 TCC（Try-Confirm-Cancel）的概念，最早是由 Pat Helland 于 2007 年发表的一篇名为《Life beyond Distributed Transactions:an Apostate’s Opinion》的论文提出。

TCC分为3个阶段

* Try 阶段：尝试执行，完成所有业务检查, 预留必须业务资源;
* Confirm 阶段：确认执行真正业务，不作任何业务检查，只使用 Try 阶段预留的业务资源，Confirm 操作要求具备幂等设计，Confirm 失败后需要进行重试;
* Cancel 阶段：取消执行，释放 Try 阶段预留的业务资源。Cancel 阶段的异常和 Confirm 阶段异常处理方案基本上一致，要求满足幂等设计。

以转账作为例子的话，通常会在Try里面冻结金额，但不扣款，Confirm里面扣款，Cancel里面解冻金额

TCC的Confirm/Cancel阶段在业务逻辑上是不允许返回失败的，如果因为网络或者其他临时故障，导致不能返回成功，需要不断地重试，直到Confirm/Cancel返回成功。

TCC特点如下：
* 并发度较高，无长期资源锁定。
* 开发量较大，需要提供Try/Confirm/Cancel接口。
* 相对于Saga，用户看不到中间态，适用于对中间状态有约束的业务
* 相对于Saga，TCC主要用于处理一致性要求较高、需要较多灵活性的短事务：