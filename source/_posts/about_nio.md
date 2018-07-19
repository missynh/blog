---
title: IO与NIO  
date: 2018-07-16  
tags: [IO，NIO]
---
#### 什么是NIO
java NIO是一个可以替代Java IO(从Java1.4开始)，Java NIO提供了与标准IO不同的IO工作方式

#### Java NIO：Channel and Buffers（通道与缓冲区）
标准的IO基于字节流和字符流进行操作的，而NIO是基于通道和缓冲区进行操作，数据总是读到缓冲区中，或者从缓冲区写入到通道中。

#### Java NIO：Non-blocking（非阻塞IO）
Java NIO可以让你非阻塞的使用IO，例如：当线程从通道中读取数据至缓冲区时，线程还是可以进行其他事情。当数据被写入到缓冲区时，线程可以继续处理它。从缓冲区写入到通道也类似。

#### Java NIO：Selecters（选择器）
Java NIO引入了选择器的概念，选择器用于监听多个通道的事件(比如：连接打开，数据到达)。因此，单个的线程可以监听多个数据通道
