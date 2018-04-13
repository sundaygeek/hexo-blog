---
title: 14-EMM Procedure 4. Service Request
date: 2016-04-03 22:34:11
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6134

# I. Introduction

这篇文档描述EMM case-4：service request过程。这个过程是当有新流量时，在idle状态不活动的UE想激活来处理流量时执行的。新用户流量可以是从UE开始的上行流量，也可以是从网络到UE的下行流量。UE的E-UTRAN资源已经被释放了，UE处于ecm-idle rrc-idle状态。所以UE为了能够接受或者发送数据，UE需要通过service request过程来转换到ecm-connected rrc-connected状态，E-UTRAN资源被重新分配。
这个文档解释了LTE网络下的service request过程。第二章根据新流量产生在哪，对service request分为不同的类型。第三章和第四章描述了每一种service request详细过程的不同。最后第五章总结在service request前后EPS实体中信息的变化。

# II. Cases of Service Request

当UE仍注册在网络，但是由于用户不活动删除了S1连接，UE没有可用的无线资源。这时候UE处于emm-registered和ecm-idle状态。如果这时候有新数据从UE产生或者从网络到UE，UE请求网络服务，转换到ecm-connected状态。ECM连接(RRC + S1 signaling connections)和e-RAB连接(DRB + S1 bearer)在控制面和用户面建立，允许UE接收或发送数据。如果网络发送数据给UE，网络会通知给UE自己的意图，让用户可以请求服务。
当UE有数据发送，或者从网络的意图中知道网络要发送数据，UE发送给MME service request消息，并转移到ecm-connected、rrc-connected状态。接着UE通过使用分配的无线和网络资源，可以接受和发送数据。service request可以由UE或者网络来触发，根据不同的触发情况，可以分类为以下几种：

 - service request case-1：UE触发的新数据。当UE有新上行数据要发送给网络。
 - service request case-2：网络触发的新数据。当网络有下行数据要放给UE。

图1展示了在service request执行前后用户面和控制面连接建立、UE和MME状态。在service request之前，用户时emm-registered ecm-idle rrc-idle状态。只有EPS分配的资源保持连接没有释放，而由E-UTRAN分配的所有资源都被释放了。在控制面S5 GTP-C and S11 GTP-C保持在连接状态。

在service request之前，用户处于EMM-Registered and ECM/RRC-Idle状态。只有EPC分配的资源没有被释放，e-utran分配的资源都释放了。在ECM连接丢失的情况下，在控制面S5 GTP-C和S11 GTP-C隧道仍然保持激活。在用户面，在下行S1承载和DRB被释放的情况下，S5承载和上行S1承载保持激活。

在service request之后，UE被e-utran分配了资源，处于emm-registered和ecm-connected rrc-connected状态。我们可以看到EPS承载的所有承载和连接以及信令连接都被建立起来用于传输数据。

![这里写图片描述](http://img.blog.csdn.net/20150923150915425)

# III. UE-triggered Service Request

图2和3描述了由UE触发的service request过程。UE的NAS通过service request消息给MME指示说UE有数据要发送。接着网络为数据传输分配资源。因为UE注册在网络，所以UE的NAS安全上下文在UE和MME中保持有效。所以UE可以发送加密和完整性保护的service request消息。当MME接收到这个消息，MME决定是否执行对用户的鉴权，尽管这个消息是加密和完整性保护的，接着eNB建立e-rab。

![这里写图片描述](http://img.blog.csdn.net/20150924081218601)

**❶  ECM连接建立**

当有数据要发送，UE给MME发送service request消息建立ECM连接。service request消息通过无线链路上建立的RRC连接和S1信令连接传送给MME。这篇文档假设在UE和MME中有有效的GUTI和NAS安全上下文。

1), 2) [UE – eNB] RRC Connection Setup
UE的NAS层提供给RRC层S-TMSI。RRC层使用S-TMSI作为UEID发送RRC connection request消息到eNB来建立RRC连接。eNB返回给UE RRC connection setup消息。

3), 4), 5) [UE -> MME] ECM Connection Setup Request
UE的NAS层发送service request消息给MME来建立ECM连接。NAS安全上下文已经存储在UE和MME中。这个消息中包含KSI-ASME，并且是加密和完整性保护的。service request消息包含在RRC connection setup complete 消息中传送给eNB的。这个消息是包含在S1AP消息（initial UE消息）中从eNB传送到MME的。这是，eNB分配eNB UE S1AP ID，并包含在initial UE消息消息中发送给MME。一旦接收到initial UE消息，MME分配MME S1AP UE ID，并和eNB之间建立S1信令连接。

**❷  UE鉴权和NAS安全建立(可选)**

6) [UE – MME – HSS] UE Authentication
在接收到service request消息后，MME执行完整性检查，如果通过，MME使用当前的NAS安全上下文传输NAS消息，并不对UE再执行鉴权流程。如果失败，MME会对UE执行鉴权流程。

7) [UE – MME] NAS Security Setup
当鉴权过程完毕后，UE和MME都生成了用于传输NAS消息的NAS安全秘钥。

![这里写图片描述](http://img.blog.csdn.net/20150924081319799)

**❸  E-RAB 建立**

在收到service request消息之后，MME通过e-rab建立过程建立DRB和下行承载。

8) [eNB <- MME] 请求E-RAB建立
一旦接收到UE发送的service request消息，MME意识到要建立e-rab。接着MME给eNB发送Initial Context Setup Request 消息，让eNB可以建立和SGW建立S1承载，和UE建立DRB。这个消息包含以下信息：

Initial Context Setup Request (E-RAB ID, KeNB, S1 S-GW TEID, MME UE S1AP ID)

 - E-RAB ID
 - KeNB: AS安全基础秘钥，用来eNB和UE建立AS安全
 - S1 S-GW TEID: 标识连接到SGW的上行S1承载
 - MME UE S1AP ID: 标识连接到MME的S1信令承载

9) [UE <– eNB] AS Security Setup
在接收到initial context setup消息后，eNB意识到要建立DRB和S1承载用于用户数据传输。在建立DRB之前，eNB执行AS安全建立过程。UE和eNB通过AS安全建立过程生成KRRCint/KRRCenc用于加密和完整性保护，生成KUPenc 用于用户数据加密。
在成功完成AS安全建立，RRC消息在无线链路上是加密和完整性保护传输的，用户数据也是加密传输的。接着eNB开始DRB建立。

10) [UE <- eNB] DRB Establishment
eNB分配一个DRB ID来创建DRB，并根据从MME接收到的e-rab QoS配置DRB QoS参数后，发送RRC connection reconfigutaion消息给UE。UE一旦接收到RRC Connection Reconfiguration消息，生成DRB和SRB2。

11) 上行传输通道可用
一旦完成步骤10生成DRB之后，就建立从UE到PGW的上行EPS承载，允许UE传输上行数据。

12) 13) & 16) [eNB -> S-GW] 建立下行S1承载
在步骤12，eNB为S1承载分配下行S1 TEID (S1 eNB TEID)，并把这个ID包含在initial context setup response消息中发送给MME，作为对Initial Context Setup Request消息的响应。MME把S1 eNB TEID包含在Modify Bearer Request消息中发送给SGW，SGW使用这个信息建立下行S1承载。接着，在步骤16，SGW通过Modify Bearer Response 消息通知MME下行S1承载建立完成。

14) 下行传输通道可用
一旦完成步骤13，从SGW到eNB的下行S1 GTP-U隧道就建立了，完成了从PGW到UE的下行EPS承载。用来传输下行数据给UE。

15) Modifying EPS Session (UE Location Registration)
在service request时如果UE的当前小区或者TA发送变化，SGW通知PGW这个变化。接着PGW通过EPS session medication过程报告给PCRF。

# IV. Network-triggered Service Request

图4和 5描述了网络触发的service request过程。这种类型的service request是当网络有下行数据要发送给UE时触发。MME不知道idle状态的UE处于什么位置，所以MME不得不通过寻呼过程来通知UE它有数据要发送，接着已经释放的承载资源可以重新建立。

![这里写图片描述](http://img.blog.csdn.net/20150924081422777)

**❶  Service Request触发**
如果SGW从PGW通过S5承载收到下行数据包，但是不能通过下行S1承载发送给eNB，因为下行S1承载已经释放了。首先SGW缓存这些数据包，并找到UE注册在哪一个MME。接着SGW给MME发送Downlink Data Notification消息通知需要为UE建立信令连接和承载。

**❷  Paging**
MME知道UE驻留在哪一个TA，但是不知道UE驻留的小区。所以MME给这个TA下的所有eNB发送寻呼消息。eNB广播接收到的寻呼消息（通过PCH信道），UE在常规监控PCH时可以接收到这个消息。

**❸  ECM连接建立**
意识到有数据要到来，UE发送service request消息来建立ECM连接。ECM建立从UE通过无线接入信道接入小区开始，接着发送RRC Connection Request消息建立RRC消息。执行过程如图2所示。

**❹  UE鉴权和NAS安全（可选）**
MME一旦接收到UE发送的service request消息，如果NAS-MAC完整性检查失败，将实行UE鉴权和生成NAS安全秘钥。

![这里写图片描述](http://img.blog.csdn.net/20150924081508534)

**❺  E-RAB 建立**
在从UE接收到service request消息后，MME通过e-rab建立过程建立DRB和下行S1承载。接着准备接收从SGW的数据包。详细过程如图3所示。

# V. EPS Entity Information: Before/After Service Request

这章将描述在service request前后EPS实体中信息的变化。所有的这些信息将分类为UE ID，UE Location，安全和EPS会话/承载信息。

##  5.1 Before Service Request
在service request触发之前，UE以为S1承载的释放而转移到emm-registered和ecm-idle rrc-idle状态。在S1承载释放后，所有EPS实体中的信息都保持相同直到service request过程执行。也就是说，E-UTRAN分配的无线资源和相关的EPS承载信令都从EPS实体中删除了。在图6中，在service request之前EPS实体中信息被标记为黑色。

![这里写图片描述](http://img.blog.csdn.net/20150924081600435)

##  5.2 After Service Request
一旦完成service request过程，UE转移到ECM/RRC-Connected状态，RRC连接、S1信令和e-rab承载都建立起来了。用于UE和PGW之间传输的所有需要的信息都存储在EPS实体中。图7描述了在service request之后，EPS实体中存储的信息。

![这里写图片描述](http://img.blog.csdn.net/20150924081642577)



# VI. Closing

这篇文档，学习了service request过程。总结了存储在EPS实体中的信息。接下来的文档将讨论TAU过程。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 2. Call Flow of Initial
Attach”, January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[3] Netmanias Technical Document, “LTE Security II: NAS and AS Security”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5903
[4] Netmanias Technical Document, “LTE EMM Procedure 3. S1 Release”, January 2014,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6110
[5] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010
