---
title: 23-LTE Charging I_Offline
date: 2018-04-03 22:36:15
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接： http://www.netmanias.com/en/?m=view&id=techdocs&no=7179

# I. Introduction

之前的PCC文档，我们的焦点在讨论关于QoS规则的PCC策略。现在我们的焦点是付费策略，特别是离线付费策略。

在LTE网络中，服务是按照承载（eg，EPC）/子系统（eg.IMS）/服务级别（eg MMS）或者是在线和离线来付费的。
对于在线付费，当用户请求网络资源，网络运行账户查询并决定是否授权用户用户使用请求的网络资源。这个情况，用户的网络资源利用被实时测量，所以说付费信息对提供的服务有实时的影响。这个方法应用在预付套餐上。（预付套餐是神马？？）
另一方面，对于离线付费，用户为已经使用过的网络资源付费。也就是说，网络通过转发用户的CDR（付费数据记录）到账单系统来报告特定用户的资源使用情况。用户使用的信息定期的收集，或者在没有会话结束时收集。所以付费信息对提供的服务没有实时性影响。
通常上，在线付费是使用在预付费套餐上，离线付费使用在后付费套餐上。例如，多数韩国的移动用户是后付费套餐使用者，他们都属于离线付费策略。这种类型套餐的使用者首选使用服务，然后他们的运营商每月再计算他们的使用情况付费。在全球范围内，预付费用户比后付费用户要多。预付费用户首先付费，然后保证他们的配额。一旦他们的配额超出，激活的会话就会终止。
这篇文档主要讨论当在承载级别后付费用户使用Internet服务时运用离线付费策略的付费过程。
这篇文档组织如下：第二章提供付费相关的接口和离线付费是怎么工作的，第三章解释离线付费的过程。第四章总结CDR参数，离线付费参数。

# II. Overview of Offline Charging

下面我们将定义LTE参考模型中付费相关的接口。图1描述了LTE付费架构和相关的接口。
![这里写图片描述](http://img.blog.csdn.net/20151021093007771)

 - 策略和付费规则功能PCRF：当决定到用户SDF的PCC策略时，PCRF选择一个付费策略适用于SDF（service data flow），并通过Gx接口转发这个策略到PGW中的PCEF（policy and charging enforcement function）。
 - PGW：PGW对用户的SDF执行PCRF转发的付费策略。在离线付费，PGW根据付费策略生成CDR，并通过Gz接口转发至OFCS（offline charging system）。在在线付费，PGW首先请求和获得配额，然后通过Gy接口和OCS（online charging system）执行信用控制来测量和报告用户的使用情况。
 - OFCS离线付费系统：一旦接收到Gz接口上PGW的CDR，OFCS后处理他们并转发至账单系统billing system、
 - OCS在线付费系统：通过Gy接口和PGW执行信用控制来给用户分配配额（eg，这个会话持续多长时间，用户交付多少数据等等）

遮普文档只关于离线付费的，图1展示了PCRF PGW OFCS怎么工作的。在我们进一步详细讨论之前，我们先简要介绍一下离线付费系统是怎么工作的。

图2提供了离线付费工作的概况，特别的付费规则的交付和CDR的传输。
![这里写图片描述](http://img.blog.csdn.net/20151021104021889)

**1）PCRF**，一旦EPS会话建立，PCRF选择一个付费策略，并嵌入到PCC策略中转发给PCRF（PGW）。同事，付费策略的名字被定为PCC规则的名字。像付费方法（在线或者离线），排序组（服务是怎么付费，每分钟100元，100M 1000元等等）这些信息作为付费规则包含在PCC规则中。在图2中，Internet会话建立了，在上面应用了offline的付费方法和rgInternet排序方法的付费规则。

**2）PGW**从PCRF接收到付费规则，PGW知道使用的是离线付费。PGW决定应该测量或者产生什么样的付费信息，通过那个接口传输这些信息。接着PGW创建默认承载，在上面应用这些付费规则，并打开CDR，准备好生成付费信息。

**3）**现在IP数据包通过PGW传输。PGW开始通过Internet SDF开始测量网络使用量，生成付费信息。

**4）**一旦EPS会话终止，PGW把这些CDR记录信息转发给OFCS。在这个实例中，每一个排序组的使用量都报告了，CDR包含以下信息：

 - 用户信息（eg用户ID，用户位置）
 - 网络/承载信息（eg服务网络ID，APN，PGW，SGW地址，请求的QoS，授权的QoS）
 - 网络资源使用量（eg传输的字节数）
 - 使用量信息（eg总的获取时间，数据包传输时间）
 - 服务信息（eg服务id，数据包过滤）


# III. Offline Charging Procedure

图3是LTE网络中离线付费过程。这个文档只关注在付费处理过程。
![这里写图片描述](http://img.blog.csdn.net/20151021105257818)

**1）【PCRF<-SPR】获得用户配置**
在用户初始附着过程中，PCRF从SPR中请求和获得用户的签约配置文件为了决定PCC规则。

**2）【PGW<-PCRF】请求会话建立**
PCRF基于用户签约配置文件决定Internet会话的付费策略。PCRF通过CCA（credit control answer消息）转发PCC规则“Internet”给PGW。

**3)【PGW】CDR创建**
PGW根据PCC规则创建默认承载，并且对这个承载执行付费策略。接着PGW通过创建CDR准备好生成付费信息。

**4）【PGW】生成付费信息**
一旦用户开始使用Internet，PGW记录用户网络资源使用情况和详细使用信息，生成付费信息。

**5）【PGW】CDR关闭**
PGW一旦在用户detach之后EPS会话结束后关闭CDR。

**6）【PGW->OFCS】CDR传输**
PGW通过data record transfer request（CDR）消息通过GTP'协议把CDR传输给OFCS。报告的信息包括每一个排序组的使用信息，数据包交付时间，字节交付时间等等。

**7）【PGW<-OFCS】CDR接收确认**
OFCS通过发送data  record transfer accept消息确认CDR的接收。

OFCS收集CDR，后处理他们，并且把他们转发给运营商计费系统。这个计费系统分析接收到的CDR来获得统计值决定要付费多少钱。接着使用这些结果向用户收费或者运营商之间账户。

# IV. Parameters for Charging Data Record (CDR)

图2和图3总结了CDR的主要参数。除了这些参数，CDR还有其他另外的付费参数。表1列出了在承载级别上使用的CDR参数。
![这里写图片描述](http://img.blog.csdn.net/20151021113658809)
![这里写图片描述](http://img.blog.csdn.net/20151021113732062)
![这里写图片描述](http://img.blog.csdn.net/20151021113756769)

# V. Closing

我们讨论了付费过程。下一个文档将讨论在线付费系统。

# References
[1] Netmanias Technical Document, “LTE Policy and Charging Control (PCC)”, August 2014,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6562
[2] 3GPP TS 32.240, “Telecommunication management; Charging management; Charging architecture
and principles”
[3] Netmanias Technical Document, “LTE Network Architecture: Basic”, July 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5904
[4] 3GPP TS 32.295, “Telecommunication management; Charging management; Charging Data Record
(CDR) transfer”
[5] Netmanias Technical Document, “EMM Procedure 1. Initial Attach - Part 2. Call Flow of Initial Attach”,
January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[6] 3GPP TS 32.298, “Telecommunication management; Charging management; Charging Data Record
(CDR) parameter description”
[7] Netmanias Technical Document, “LTE Charging: Online”, TBD
[8] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010