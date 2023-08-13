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