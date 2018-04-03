---
title: 'LTE Discontinuous Reception (DRX) in RRC_IDLE: Part 2'
date: 2018-04-03 23:10:06
tags:
    - LTE
    - 通信
    - DRX
categories:
    - 通信
    - LTE
---

原文链接
http://lteuniversity.com/get_trained/expert_opinion1/b/prakash/archive/2013/04/24/discontinuous-reception-drx-in-rrc-idle-part-2.aspx


在空闲模式下的UE监听PDCCH信道P-RNTI用于寻呼从基站接收寻呼消息。 UE需要只在某些特定的UE寻呼时机来监视PDCCH，即只在特定的无线电帧特定子帧。在其他时间，UE可以进入睡眠模式，以节省电池电量。该DRX参数和各DRX参数的意义，在这个博客的第一部分讨论。一个DRX参数“NB”是用于寻呼容量在小区中的决定因素。让我们来讨论不同的方案在这个博客考虑NB的不同的价值观和理解上的寻呼容量这个参数的影响。

##  方案1：NB = T / 2

  ![这里写图片描述](http://img.blog.csdn.net/20151122142842008)

观察：

 - UE需要唤醒每32毫秒，并查找寻呼消息（共32个无线帧UE的DRX周期）
 - 其中每2无线帧（NB = 16），可在UE的DRX周期的寻呼。即总共16个寻呼帧单元中配置的
 - 按照寻呼帧的一个寻呼子帧（NS = 1），可用于寻呼
 - 根据预定义的图案对于FDD，为Ns个= 1，i_s = 0，子帧9被用于寻呼在寻呼帧
 - 总的跨哪些UE基于UE标识分布式16寻呼时机（NB = 16）每UE的DRX周期。

  ![这里写图片描述](http://img.blog.csdn.net/20151122143018434)

##  方案2：NB = 2T

  ![这里写图片描述](http://img.blog.csdn.net/20151122143025459)

观察：

 - 每一个无线帧是寻呼帧
 - 每PF（NS = 2）2子帧中的UE的DRX周期用于寻呼
 - 根据预定义的图案对于FDD，为Ns个= 2和i_s = 0，子帧0和4被用于寻呼在寻呼帧和寻呼时机用于UE中的讨论，是子帧4
 - 32寻呼帧和64寻呼场合（NB = 64）是内UE的DRX周期跨越哪些UE基于UE标识分布式可用。

 ![这里写图片描述](http://img.blog.csdn.net/20151122143033644) 

##  方案3：NB = 4T

 ![这里写图片描述](http://img.blog.csdn.net/20151122143040746) 

观察：

 - 每一个无线帧是寻呼帧
 - 每PF（NS = 4）的4个子帧中的UE的DRX周期用于寻呼
 - 根据预定义的图案用于FDD为Ns个= 4，子帧0,4,5和9用于寻呼在用于UE中讨论的寻呼帧和寻呼时机是子帧5
 - 32寻呼帧和128寻呼场合（NB = 128）是内UE的DRX周期可以跨哪些UE基于UE标识分布式

![这里写图片描述](http://img.blog.csdn.net/20151122143048985)