---
title: 2020全国硕士研究生招生考试计算机科学与技术学科联考大纲（408）：操作系统
category: 
- Dream
tag: 
- NPEE
- 操作系统
---
【考察目标】

1. 掌握操作系统的基本概念、基本原理和基本功能，理解操作系统的整体运行过程。
2. 掌握操作系统进程、内存、文件和 I/O 惯例的策略、算法、机制以及相互关系。
3. 能够运用所学的操作系统原理、方法与技术分析问题和解决问题，并能利用 C 语言描述相关算法。

<!-- more -->

## 操作系统概述

1. 操作系统的概念、特征、功能和提供的服务
2. 操作系统的发展与分类
3. 操作系统的运行环境
   1. 内核态与用户态
   2. 中断、异常
   3. 系统调用
4. 操作系统体系结构

---

## 进程管理

1. 进程与线程
    1. 进程概念  
    process（进程）: an abstraction of a running program. A process is just an instance of an executing program, including the current values of the program counter, registers, and variables.  
    pseudoparallelism（伪并行）  
    multiprocessor systems（多处理器系统）: which have two or more CPUs sharing the same physical memory.  
    multiprogramming（多道程序设计）：the real CPU switches back and forth from process to process.  
    daemon（守护进程）: Processes that stay in the background to handle some activity  
    2. 进程的状态与转换
    3. 进程控制  
    Four principal events cause processes to be created:
       - System initialization.
       - Execution of a process-creation system call by a running process.
       - A user request to create a new process.
       - Initiation of a batch job.  
    4. 进程组织
    5. 进程通信  
    共享存储系统，消息传递系统，管道通信。
    6. 线程概念与多线程模型
2. 处理机调度
   1. 调度的基本概念
   2. 调度时机、切换与过程
   3. 调度的基本准则
   4. 调度方式
   5. 典型调度算法  
   先来先服务调度算法，短作业（短进程、短线程）优先调度算法，时间片轮转调度算法，优先级调度算法，高响应比优先调度算法，多级反馈队列调度算法。
3. 同步与互斥
   1. 进程同步的基本概念
   2. 实现临界区互斥的基本方法
   软件实现方法，硬件实现方法。
   1. 信号量
   2. 管程
   3. 经典同步问题  
   生产者-消费者问题，读者-写者问题，哲学家进餐问题。
4. 死锁
   1. 死锁的概念
   2. 死锁处理策略
   3. 死锁预防
   4. 死锁避免  
   系统安全状态，银行家算法。
   1. 死锁检测和解除

## 内存管理

1. 内存管理基础
   1. 内存管理概念  
   程序装入与链接，逻辑地址与物理地址空间，内存保护。
   2. 交换与覆盖
   3. 连续分配管理方式
   4. 非连续分配管理方式  
   分页管理方式，分段管理方式，段页式管理方式。
2. 虚拟内存管理
   1. 虚拟内存基本概念
   2. 请求分页管理方式
   3. 页面置换算法  
   最佳置换算法 (OPT)，先进先出置换算法 (FIFO)，最近最少使用置换算法 (LRU)，时钟置换算法 (CLOCK)。
   4. 页面分配策略
   5. 工作集
   6. 抖动

## 文件管理

1. 文件系统基础
   1. 文件概念
   2. 文件的逻辑结构  
   顺序文件，索引文件，索引顺序文件。
   3. 目录结构  
   文件控制块和索引节点，单级目录结构和两级目录结构，树形目录结构，图形目录结构。
   4. 文件共享
   5. 文件保护  
   访问类型，访问控制。
2. 文件系统实现
   1. 文件系统层次结构
   2. 目录实现
   3. 文件实现
3. 磁盘组织与管理
   1. 磁盘的结构
   2. 磁盘调度算法
   3. 磁盘的管理

## 输入输出 (I/O) 管理

1. I/O 管理概述
   1. I/O 控制方式
   2. I/O 软件层次结构
2. I/O 核心子系统
   1. I/O 调度概念
   2. 高速缓存与缓冲区
   3. 设备分配与回收
   4. 假脱机技术 (SPOOLing)

[返回大纲](https://nachtgeistw.github.io/Berksey/dream/2019/08/27/NPEE-Outline-408/)
