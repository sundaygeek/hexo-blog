---
title: 13-EMM Procedure 3. S1 Release
date: 2016-04-03 22:19:40
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6110


----------
# I. Introduction

这个文档 描述了EMM case-3，当UE不活动状态时释放S1连接。当注册到网络的UE处于不活动状态，没有使用任何服务（并不意味着不适用eNB分配的无线资源）。所以网络需要释放这些相关的无线接入相关的资源，并删除相关的信息（ID，QoS参数等等）。
从网络的角度看，S1释放意味着和UE有关的控制面S1信令和RRC连接，以及用户面下行S1承载和DRB。
但是，从UE的角度来看，它意味着在控制面和用户面丢失RRC连接和DRB。一旦S1连接释放，MME和UE之间的ECM连接也丢失了，在eNB中和UE相关的所有上下文都被删除了。接着用户从ecm-connected状态转移到ecm-idle状态，但是一直保持emm-registered状态。
这个文档解释了LTE网络中因为用户的不活动引起的S1释放。第二章描述了S1释放是怎么执行的，第三章描述了在S1释放过程之后信息单元是怎么变化的。

# II. S1 Release due to User Inactivity

S1释放可以被eNB或者MME触发。eNB触发的释放原因有：

 - 用户不活动
 - RRC信令完整性校验失败（再三）
 - 因为UE产生信令连接释放而释放
 - 未知错误
 - O&M干预

MME触发的释放原因有：

 - 鉴权失败
 - detach
 - 不允许CSG小区

另外，S1释放可以被其他另外两个因素，例如处理超负荷，没有足够可用的用户面处理资源等等。
图1展示了在S1释放前后，控制面和用户面UE和MME的状态。在释放前，EPS承载和信令连接建立来支持用户网络之间的流量传输。EPS承载包括DRB，S1承载，S5承载。信令连接包含ECM（RRC+S1信令连接），S11和S5连接。UE和MME都在emm-registered状态和ecm-connected状态，UE和eNB在rrc-connected状态。
在S1释放后，用户面的DRB和下行S1承载被释放，在控制面ECM（RRC+S1信令连接）连接丢失，释放E-UTRAN资源。应该注意的是，这时候只有下行S1承载释放了，上行的S1承载还在保持着。
S1释放不同于在detach过程中释放一样。在detach过程中，所有分配给UE的资源都被释放，UE转移到emm-deregistered状态。而在S1释放过程中，只有由无线接入网络分配的这些资源释放，而被EPC分配的资源则没有释放。所以UE保持在emm-registered状态，转移到ecm-idle状态。在接下来，当有上下行用户流量时，ECM连接和DRB/S1承载（下行）建立起来，UE状态转到ecm-connected并传输用户流量。

![这里写图片描述](http://img.blog.csdn.net/20150922144852795)

图2描述了由eNB触发的一旦用户不活动时S1释放过程。在由MME触发的S1释放情景中步骤1和2保留。

![这里写图片描述](http://img.blog.csdn.net/20150922144951281)

1）【eNB->MME】请求UE上下文释放
一旦检测到用户不活动，eNB发送给MME UE Context Release Request消息，伴随着释放的原因，来释放UE上下文。

2）【MME->SGW】请求S1承载释放
MME通过发送给SGW Release Access Bearers Request消息来请求SGW释放和eNB关联的资源，S1的下行端口。通过这个消息，通知SGW没有下行流量传输给UE了。

3）【SGW】下行S1承载释放
SGW释放和UE关联的所有下行S1承载（eNB相关的资源，包含eNB分配的下行S1 TEID等等）但是保留着上行S1承载资源（SGW相关的资源，包括上行S1 TEID和自己分配的上行S1 TEID等等）。所以当上行数据包到达时，eNB可以保持上行S1 TEID，通过S1承载无延迟的传输这些数据包。

4）【MME<-SGW】响应S1 Bearer Release Request
SGW通过发送Release Access Bearers Response消息来确认下行S1承载资源已经被释放了。之后，如果有到达UE的下行数据包到达，SGW缓存他们，然后在下行S1承载重建之后再传输。详细的步骤将在下面文档中解释EMM case-4、

5）【eNB<-MME】UE Context Release Command
MME给eNB发送UE Context Release Command消息来释放存储在eNB中的UE上下文。

6) [UE <- eNB] RRC Connection Release
eNB一旦从MME接收到命令，将删除eNB具有所有UE上下文。如果RRC连接还没有释放，eNB发送给UE RRC Connection Release消息来释放。通过这个消息，eNB释放和UE相关的所有无线资源和承载，并伤处UE上下文。

7) [eNB -> MME] UE Context Release Complete
eNB给MME发送UE Context Release Complete message作为步骤5的响应。MME确认UE所有的上下文已经删除了。

8) 【MME】 S1 Release
MME释放在UE上下文中所有eNB相关的信息，除了上行S1承载信息。但是保留和eNB不关联的信息。

# III. EPS Entity Information: Before/After S1 Release

这章将学习在S1释放前后EPS实体中信息的变化。所有的信息分类为UE ID，UE Location，安全和EPS会话/承载信息。

##  3.1 Before S1 Release
在S1释放之前，UE连接到网络，在EMM-Registered, ECMConnected and RRC-Connected 状态下使用服务。所有的EPS实体保持着和初始附着后相同的信息。图3列出来在S1释放之前所有EPS实体的信息。

![这里写图片描述](http://img.blog.csdn.net/20150922145045998)

##  3.2 After S1 Release
在S1释放之后，UE仍然注册在网络上，但是是在EMM-Registered, ECM-Idle and RRC-Idle状态，不使用任何服务。因为UE和任何eNB没有连接，所有UE请求连接eNB和传输数据需要的信息被释放。在控制面，ECM连接（RRC和S1信令连接）被释放，在用户面DRB和下行S1承载被释放。图4列出了在S1释放之后每一个实体中的信息。在S1释放的信息被标记为灰色。

![这里写图片描述](http://img.blog.csdn.net/20150922145116068)

在UE中删除的信息：

 - C-RNTI：在UE要连接小区中标识UE的ID。
 - ECGI：在UE要连接的小区上的信息
 - DRB ID：在无线链路上的EPS承载的ID。由eNB分配给UE。
 - AS Security Info: 在UE和eNB之间的AS安全上下文。

在eNB中删除的信息：

 - 所有的信息

在MME删除的信息：

 - S1AP UE ID：用在S1信令连接的UE ID信息(eNB S1AP UE ID and MME S1AP UE ID)
 - ECGI: 在UE要连接的小区上的信息
 - S1 TEID (DL): 在下行S1承载上使用的TEID信息

在SGW删除的信息

 - S1 TEID (DL): 在下行S1承载上使用的TEID信息
 - ECGI: 在UE要连接的小区上的信息


# IV. Closing

我们已经讨论完了当用户不活动时S1释放过程。接下来我们学习service request过程。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 2. Call Flow of Initial
Attach”, January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[3] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010