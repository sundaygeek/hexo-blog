---
title: Hexagon QDSP6介绍
date: 2018-05-15 17:26:15
tags:
 - 高通
---

Hexagon（QDSP6）是由Qualcomm开发的基于DSP的32位多线程CPU架构。根据2012年的估算，高通公司在2011年内在其SoC（平均每个SoC 2.3个DSP内核）中出货了12亿个DSP内核，并计划在2012年出现15亿个内核，这使QDSP出货量最大的DSP架构。2011年出货的DSP核心数量为10亿个，拥有90％的IP许可的DSP市场）。
Hexagon架构旨在通过各种应用提供低功耗的性能。它具有诸如硬件辅助多线程，特权级别，VLIW（超长指令字），SIMD（单指令多数据），[4]以及面向高效信号处理的指令等功能。 CPU可以按顺序将每条时钟最多4条指令（数据包）分配到4个执行单元。硬件多线程实现为桶时间多线程 - 线程在每个周期以循环方式切换，因此在V5之前，600 MHz物理内核呈现为三个逻辑200 MHz内核。Hexagon V5切换到动态多线程（DMT），并且在L2缺失，中断等待或特殊指令时使用线程切换。
Hexagon Linux的端口在虚拟机管理程序层（“Hexagon Virtual Machine”）下运行，并与3.2版本的内核合并。原始的管理程序是封闭的，2013年4月，Qualcomm根据BSD格式的许可证发布了针对QDSP6 V2和V3的最小的开放源代码管理程序实现，“Hexagon MiniVM”
Tony Linthicum在3.1版LLVM（低级虚拟机）中增加了对Hexagon的支持。GCC和binutils也有一个非FSF维护的分支。
自2006年以来，海克斯康DSP包含在Snapdragon SoC中。在Snapdragon S4（MSM8960和更新版本）中，有三个QDSP内核，两个在调制解调器子系统和一个在多媒体子系统中的Hexagon内核。调制解调器内核仅由Qualcomm编程，只允许用户编程多媒体内核。
它们也用于Qualcomm的一些femtocell处理器，包括FSM9832。

