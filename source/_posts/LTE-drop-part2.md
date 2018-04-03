---
title: LTE-drop-part2
date: 2018-04-03 23:17:25
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
在前面的博客，我们描述了UE执行评估无线电链路的状况的活动，以确定它是否是in-syn或不同步。根据供应商的实现，一个不同步指示的，可能会导致下降的会话。

在这篇博客中，我们将集中在由eNodeB进行的活动在检测到无线链接失败时。

失败的eNodeB可检测（再次，这些可以是供应商特定的）的类型是：
A）DL失败（RLC失败）
B）UL失败（物理层失败）。

**在RLC层DL失败**

在RLC层发生了故障时所通过空中发送数据或信令不成功并且所述RLC层停止尝试。当数据被发送的空气，但不正确地接收，接收机将发送NACK。此外，发射机可以发送请求对所有接收到的分组的确认，通过设置轮询位。接收机将发送所有接收到的分组的列表。如果没有接收到发送的分组时，它被认为是丢失的。在这两种情况下，发射器将重新传送。见下图。

  ![这里写图片描述](http://lteuniversity.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-00-00-92/1581.Fig1.png)

这个过程可以重复，但在某些时候发射器将放弃该数据包。如果出现这种情况，发射器宣称无线链路失败，并启动程序，以传达给对方。

参数MaxRetxThreshold确定次数的分组被重发在RLC层中的下行链路的数目。如果这个数字达到中，eNodeB声明的DL RLC失败和“破坏”为示于下面的图像的背景。

  ![这里写图片描述](http://lteuniversity.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-00-00-92/0511.Fig2.jpg)

 

**在物理层UL失败**

并非所有供应商的实现支持这种类型的故障检测。它本质上包括测量由UE在UL发送的探测参考信号（SRS）的功率。如果功率低于给定的SINR阈值，计时器被启动。如果SINR仍在定时器的整个持续时间的所述SINR的阈值，则e节点B宣告所述UL为不同步，并进入“杀死”的上下文。如果SRS的SINR变为上面的定时器持续时间期间的第二指定阈值，所述UL被说成是在型同步和没有动作的方式实施。

下面，由eNodeB的执行行动都显示检测到UL物理层故障时。

![这里写图片描述](http://lteuniversity.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-00-00-92/7455.Fig3.png)
你是对的！！！但想想后果了！

是的，增加maxretxthreshold的值可能会导致由于RLC DL失败降会话的数量减少。

但是，为了避免大量滴，做的最好的事情就是清理射频环境您的网络。
