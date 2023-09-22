---
layout: post
title: linux kernel
date: 2023-03-09
description: 
tags: Linux
categories: programming
---

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/linux-overview.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
  Linux内核的高层次概述以及完整的Linux系统中的各个层次
</div>

## 虚拟文件系统
为支持各种本机文件系统，且在同时允许访问其他操作系统的文件，Linux内核在用户进程（或C标准库）和文件系统实现之间引入了一个抽象层。该抽象层称之为虚拟文件系统（Virtual File System），简称VFS。
内核支持40多种文件系统，其来源各种各样：来自MS-DOS的FAT文件系统、UFS（Berkeley UNIX）、用于CD-ROM的iso9660、网络文件系统（如coda和NFS）和虚拟的文件系统（如proc）。

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/linux-vfs.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
  用作文件系统抽象的VFS层
</div>

### 文件系统类型
-  基于磁盘的文件系统（Disk-based Filesystem）是在非易失介质上存储文件的经典方法，用以
在多次会话之间保持文件的内容。
- 虚拟文件系统（Virtual Filesystem）在内核中生成，是一种使用户应用程序与用户通信的方法。
proc文件系统是这一类的最佳示例。它不需要在任何种类的硬件设备上分配存储空间。相反，内核建
立了一个层次化的文件结构，其中的项包含了与系统特定部分相关的信息。
- 网络文件系统（Network Filesystem）这种文件系统允许访问另一台计算机上的数据，该计算机通过网络连接到本地计算机.这意味着内核无需关注文件存取、数据组织和硬件通信的细节，这些由远程计算机的内核处理。对此类文件系统中文件的操作都通过网络连接进行。在进程向文件写数据时，数据使用特定的协议（由具体的网络文件系统决定）发送到远程计算机。接下来远程计算机负责存储传输的数据并通知发送者数据已经到达。
尽管如此，即使在内核处理网络文件系统时，仍然需要文件长度、文件在目录层次中的位置以及文件的其他重要信息。它必须也提供函数，使得用户进程能够执行通常的文件相关操作，如打开、读、删除等。由于VFS抽象层的存在，用户空间进程不会看到本地文件系统与网络文件系统之间的区别。

### 无持久存储的文件系统
统上，文件系统用于在块设备上持久存储数据。但也可以使用文件系统来组织、提供或交换并不存储在块设备上的信息，这些信息可以由内核动态生成。

#### proc文件系统（proc filesystem）

它使得内核可以生成与系统的状态和配置有关的信息。该信息可以由用户和系统程序从普通文件读取，而无需专门的工具与内核通信。在某些情况下，一个简单的cat命令就足够了。数据不仅可以从内核读取，还可以通过向proc文件系统的文件写入字符串，来向内核发送数据。
该方法利用了一个虚拟文件系统“即时”产生文件信息。换句话说，只有发出读操作请求时，才会生成信息。对于此类文件系统，不需要专用的硬盘分区或其他块存储设备。

- 特定于进程的数据

每个系统进程，无论当前状态如何，都有一个对应的子目录（与其PID同名），包含了该进程的有关信息。顾名思义，进程数据系统（process data system，简称proc）的初衷就是传递进程数据。特定于进程的目录保存了哪些信息？简单的一个ls-l命令，就能看到一些信息：
```
$ cd /proc/7748 
$ ls -l
```

#### sysfs文件系统
sysfs是一个向用户空间导出内核对象的文件系统，它不仅提供了察看内核内部数据结构的能力，还可以修改这些数据结构。特别重要的是，该文件系统高度层次化的组织：sysfs的数据项来源于内核对象（kobject），而内核对象的层次化组织直接反映到了sysfs的目录布局中,由于系统的所有设备和总线都是通过kobject组织的，所以sysfs提供了系统的硬件拓扑的一种表示。
sysfs的标准装载点是/sys。


## 进程间通信（Inter-Process Communication, IPC）

### 消息队列
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/linux-ipc-msg-queue.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
  消息队列的功能原理
</div>

产生消息并将其写到队列的进程通常称之为发送者，而一个或多个其他进程（逻辑上称之为接收者）则从队列获取信息。各个消息包含消息正文和一个（正）数，以便在消息队列内实现几种类型的消息。接收者可以根据该数字检索消息，例如，可以指定只接受编号1的消息，或接受编号不大于5的消息。在消息已经读取后，内核将其从队列删除。即使几个进程在同一信道上监听，每个消息仍然只能由一个进程读取。

同一编号的消息按先进先出次序处理。放置在队列开始的消息将首先读取。但如果有选择地读取
消息，则先进先出次序就不再适用。

相对于管道而言，更加适合进程之间较为频繁的交换数据。

但是消息队列也有缺点，消息队列不适合比较大的数据的传输，因为在内核中每个消息体都有一个最大长度的限制，同时所有队列所包含的全部消息体的总长度也是有上限。在 Linux 内核中，会有两个宏定义 MSGMAX 和 MSGMNB，它们以字节为单位，分别定义了一条消息的最大长度和一个队列的最大长度。

消息队列通信过程中，存在用户态与内核态之间的数据拷贝开销，因为进程写入数据到内核中的消息队列时，会发生从用户态拷贝数据到内核态的过程，同理另一进程读取内核中的消息数据时，会发生从内核态拷贝数据到用户态的过程。

### 共享内存
消息队列的读取和写入的过程当中，都会有发生用户态与内核态之间的消息拷贝过程。那共享内存的方式，就很好的解决了这一问题。

现代操作系统，对于内存管理，采用的是虚拟内存技术，也就是每个进程都有自己独立的虚拟内存空间，不同进程的虚拟内存映射到不同的物理内存中。所以，即使进程 A 和 进程 B 的虚拟地址是一样的，其实访问的是不同的物理内存地址，对于数据的增删查改互不影响。

共享内存的机制，就是拿出一块虚拟地址空间来，映射到相同的物理内存中。这样这个进程写入的东西，另外一个进程马上就能看到了，都不需要拷贝来拷贝去，传来传去，大大提高了进程间通信的速度。

共享内存更强大，但是也更复杂！

### 信号量
为了防止多进程竞争共享资源，而造成的数据错乱，所以需要保护机制，使得共享的资源，在任意时刻只能被一个进程访问。正好，信号量就实现了这一保护机制。

信号量其实是一个整型的计数器，主要用于实现进程间的互斥与同步，而不是用于缓存进程间通信的数据。

### 信号
信号是进程间通信唯一的异步通信机制，一旦有信号产生，我们就必须执行下列操作之一：

- 默认操作
- 捕捉
- 忽略

kill命令根据PID向进程发送信号。信号通过-s sig指定，是一个正整数，最大长度取决于处理器类型。该命令有两种最常用的变体：一种是kill不指定信号，实际上是要求进程结束（进程可以忽略该信号）；另一种是kill -9，等价于在死刑批准上签字（导致某些进程死亡）。

init进程属于特例。内核会忽略发送给该进程的SIGKILL信号。因为该进程对整个系统尤其重要，不能强制结束该进程，即使无意结束也不行。

### 管道
shell用户可能比较熟悉管道，在命令行上可以如下使用：
```
$ prog | ghostscript | lpr- 
```

这里将一个进程的输出用作另一个进程的输入，管道负责数据的传输。顾名思义，管道是用于交换数据的连接。一个进程向管道的一端供给数据，另一个在管道另一端取出数据，供进一步处理。几个进程可以通过一系列管道连接起来。
在通过shell产生管道时，总有一个读进程和一个写进程。应用程序必须调用pipe系统调用产生管道。该调用返回两个文件描述符，分别用于管道的两端，即分别用于管道的读和写。

### Unix域协议
Unix域协议是进程间通信 (IPC)的一种形式，可以通过与网络通信中使用的相同Socket API来访问它们。下图的左边表示使用socket写成的客户程序和服务器程序，它们在同一台主机上利用TCP/IP协议进行通信。下图的右边表示用socket写的利用Unix域协议进行通信的客户程序和服务器程序。


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/linux-ipc-socket.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
  使用TCP/IP协议和Unix域协议的客户程序与服务器程序
</div>
