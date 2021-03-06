---
title: "Part-0 操作系统介绍"
date: 2022-04-08T21:43:31+08:00
draft: false
slug: operating-system-introduction
---

操作系统负责让程序运行变得容易（甚至允许你同时运行多个程序），允许程序共享内存，让程序能够与设备交互，以及其他类似的有趣的工作。

要做到这一点，操作系统主要利用一种通用的技术，我们称之为虚拟化（virtualization）。也就是说，操作系统将物理（physical）资源（如处理器、内存或磁盘）转换为更通用、更强大且更易于使用的虚拟形式。

## 2.1 虚拟化CPU

在一些硬件的帮助下，操作系统负责提供一种系统拥有非常多虚拟CPU的假象。。将单个 CPU（或其中一小部分）转换为看似无限数量的 CPU，从而让许多程序看似同时运行，这就是所谓的虚拟化 CPU（virtualizing the CPU）。

## 2.2 虚拟化内存

每个进程访问自己的私有虚拟地址空间（virtual address space）（有时也称为地址空间，address space），操作系统以某种方式映射到机器的物理内存上.

## 2.3 并发

并发问题在同时（并发（concurrency）地）处理很多事情是出现且必须解决。并发问题首先出现在操作系统中，也出现在现代多线程程序中。

## 2.4 持久性

操作系统中操理磁盘的软件通常称为文件系统（file system）。因此它负责以可靠和高效的方式，将用户创建的任何文件（file）存储在系统的磁盘上。

出于性能方面的原因，大多数文件系统首先会延迟这些写操作一段时间，希望将其批量分组为较大的组。为了处理写入期间系统崩溃的问题，大多数文件系统都包含某种复杂的写入协议，如日志（journaling）或写时复制（copy-on-write），仔细排序写入磁盘的操作，以确保如果在写入序列期间发生故障，系统可以在之后恢复到合理的状态。

## 2.5 设计目标

1. 建立一些抽象（abstraction），让系统方便和易于使用。

2. 提供高性能，换言之，我们的目标是最小化操作系统的开销。

3. 在应用程序之间以及在OS和应用程序之间提供保护（protection）。