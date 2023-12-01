---
layout: post
title: linux network
date: 2023-03-11
description: 
tags: Linux
categories: programming
---

## 内核是如何接收网络包的
当用户执行完recvfrom调用后，用户进程就通过系统调用进行到内核态工作了。如果接收队列没有数据，进程就进入睡眠状态被操作系统挂起。
1. 数据帧从外部网络到达网卡
2. 网卡把帧DMA到内存
3. 硬中断通知CPU
4. CPU响应硬中断，简单处理后发出软中断
5. ksoftirqd线程处理软中断，调用网卡驱动注册的poll函数开始收包
6. 帧被从RingBuffer上摘下来保存为一个SKB
7. 协议层开始处理网络帧，处理完后的数据data被放到Socket的接收队列中
8. 内核唤醒用户进程

* RingBuffer到底是什么, RingBuffer为什么会丢包？

网卡在收到数据的时候以DMA的方式将包写到RingBuffer中。软中断收包的时候来这里把SKB取走，并申请新的SKB重新挂上去。

RingBuffer的大小是可以设置的，长度可以通过ethtool工具查看。

```
$ ethtool -g ens3
Ring parameters for ens3:
Pre-set maximums:
RX:		256
RX Mini:	n/a
RX Jumbo:	n/a
TX:		256
Current hardware settings:
RX:		256
RX Mini:	n/a
RX Jumbo:	n/a
TX:		256
```

如果内核处理不及时导致RingBuffer满了，那后面新来的数据包就会被丢弃，通过ethtool或ifconfig工具可以查看是否有RingBuffer溢出发生。

```
$ ethtool -S  ens3
NIC statistics:
     rx_queue_0_packets: 857
     rx_queue_0_bytes: 306826
     rx_queue_0_drops: 0
     rx_queue_0_xdp_packets: 0
     rx_queue_0_xdp_tx: 0
     rx_queue_0_xdp_redirects: 0
     rx_queue_0_xdp_drops: 0
     rx_queue_0_kicks: 1
     tx_queue_0_packets: 668
     tx_queue_0_bytes: 105070
     tx_queue_0_xdp_tx: 0
     tx_queue_0_xdp_tx_drops: 0
     tx_queue_0_kicks: 634
```

修改RingBuffer大小, 不过改大之后会增加处理网络包的延时。另外一种解决思路更好，那就是让内核处理网络包的速度更快一些。

```
$ ethtool -G ens3 rx 4096 tx 4096
```

* ksoftirqd内核线程是干什么的？

机器上有几个核，内核就会创建几个ksoftirqd线程出来。
```
$ ps -ef | grep ksoftirqd
root          13       2  0 20:50 ?        00:00:00 [ksoftirqd/0]
```

ksoftirqd内核线程包含了所有的软中断处理逻辑。

```
$ cat /proc/softirqs
                    CPU0
          HI:          0
       TIMER:      21167
      NET_TX:          1
      NET_RX:       1908
       BLOCK:      11021
    IRQ_POLL:          0
     TASKLET:         74
       SCHED:          0
     HRTIMER:          0
         RCU:      22435
```

* 为什么网卡开启多队列能提升网络性能

现在主流网卡基本上都是支持多队列的，通过ethtool可以查看：

```
$ ethtool -l  ens3
Channel parameters for ens3:
Pre-set maximums:
RX:		n/a
TX:		n/a
Other:		n/a
Combined:	4
Current hardware settings:
RX:		n/a
TX:		n/a
Other:		n/a
Combined:	1
```

通过sysfs文件系统可以看到真正生效的队列数。

```
$ ls /sys/class/net/ens3/queues/
rx-0  tx-0
```

如果想加大队列数，ethtool也可以搞定

```
$ ethtool -L ens3 combined 2
```

通过/proc/interrupts可以看到该队列对应的硬件中断号。再通过该中断号对应的smp_affinity可以查看到亲和的CPU核是哪一个？

```
$ cat /proc/interrupts
...

$ cat /proc/irq/<中断号>/smp_affinity
8
```
这个亲和性是通过二进制中的比特位来标记的。例如8是二进制的1000， 第4位是1，代表是第4个CPU核心-CPU3

每个队列都有独立的，不同的中断号。所以不同的队列可以向不同的CPU发起硬中断通知。且相应的软中断也是由这个核处理的。

所以工作中，如果网络包的接收频率高而导致个别核si偏高，那么通过加大网卡队列数，并设置每个队列中断号上的smp_affinity, 将各个队列的硬中断打散到不同的CPU就行了。


* tcpdump是如何工作的

tcpdump工作在设备层，是通过虚拟协议栈的形式工作的。将抓包函数以协议的形式挂到ptype_all上。

当收包的时候，驱动在将包送到协议栈（ip_rcv， arp_rcv等）之前，将包先送到ptype_all抓包点。

* iptable/netfilter是在哪一层实现的？

netfilter主要是在IP，ARP层实现的。如果配置过于复杂的规则，则会消耗过多CPU，加大网络延迟。

* tcpdump能否抓到被iptable封禁的包？

tcpdump工作在设备层，而netfilter工作在IP/ARP层，
收包时，iptable封禁规则不影响tcpdump的抓包。
发包过程则相反，netfilter过滤后，tcpdump看不到被封禁的包。

* 网络接收过程中的CPU开销如何查看

在网络包的接收处理过程中，主要工作集中在硬中断和软中断上，二者的消耗都可以通过top命令查看。

其中hi是cpu处理硬中断的开销，si是CPU处理软中断的开销，都是以百分比的形式来展示的。
