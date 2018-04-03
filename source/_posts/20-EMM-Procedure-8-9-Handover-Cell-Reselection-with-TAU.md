---
title: 20-EMM Procedure 8&9. Handover & Cell Reselection with TAU
date: 2018-04-03 22:35:38
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6324

# I. Introduction

当UE接入网络时，MME通过TAI列表分配一组TA。UE在这些TA中移动不需要给网络报告自己的位置。网络不知道UE驻留在哪个小区，但是知道在哪个TA。所以如果idle状态的UE有电话或者数据包到达，网络在UE注册的这些TA中发起寻呼。分配给UE的TAI列表是UE已经注册的区域列表。当UE移动到不在TAI列表中的TA时，UE必须执行TAU过程来请求TA更新，网络就可以更新UE的TAI列表并分配更新过的列表。

这篇文档描述的是UE移动到没有注册过的TA时的过程（EMM case-8）。在这个case-8中，在连接模式(EMM-Registered, ECM-Connected, RRC-Connected)的UE移动到另外一个TA。在这个移动过程中，从source eNB到target eNB的切换过程如EMM case-6一样。不同的地方在于，UE接着需要在target eNB的新小区上执行TAU过程。在idle模式(EMM-Registered, ECM-Idle, RRC-Idle)状态下的UE移动到另外一个TA时，服务小区重选的过程和EMM case-7一样。不同的是，在小区重选后，必须在UE新驻留的小区上进行TAU过程。

这个文档组织如下：第二章解释EMM  case-8过程，第三章描述EMM case-9过程，在第四章我们学习在这些过程前后EPS实体的信息有什么不同。

# II. EMM case 8. handover with TAU

图1展示了连接模式下的UE移动到没有注册过程的TA时执行的过程。当UE初始附着到网络，MME为UE选择TA1和TA2，并在attach accept消息中包含TAI列表={TAI1=1,TAI=2}。UE通过eNB2 cell5获得服务。现在移动到TA3 eNB5 cell13。这里我们假设eNB2和eNB5连接到同一个MME和SGW。

![这里写图片描述](http://img.blog.csdn.net/20151010123510098)

**handover**
在上图中，随着UE移动到eNB5，handover事件触发。UE测量服务小区和邻区的信号强度，并把结果发送给eNB2，包含在measurement report消息中。首先source eNB选择切换的类型 X2 or S1.在X2切换中，是source eNB选择target eNB，而在S1切换中是MME选择target eNB。接着切换在eNB2和eNB5之间执行。

**TAU**
**1）【UE->MME】TAU request**
当切换到target eNB时，UE知道eNB5不属于MME分配的TAs，接着只要切换一完成，UE就发送TAU request消息(Update Type=TA Updating, GUTI, Last Visited TAI, KSI ASME, NAS-MAC)给MME，请求TA更新。这时，这个消息包含如下信息：
TAU Request (Update Type=TA Updating, GUTI, Last Visited TAI, KSI ASME, NAS-MAC)

 - Update Type: indicates the TAU type. Set as TA Updating unless UE is handed over to a registered TA
 - GUTI: UE ID previously allocated by MME. Used by MME for UE identification.
 - Last Visited TAI: TAI reported through TAU Request last time
 - KSIASME: index for KASME, the NAS security base key
 - NAS-MAC: message authentication code used in protecting the integrity of TAU Request with NAS integrity Key (KNASint).

TAU消息通过UL information transfer消息从 UE发送到eNB，通过uplink NAS transfer消息(NAS-PDU (TAU Request), ECGI, TAI)从eNB发送到MME。uplink NAS transfer消息包含TAU请求消息和当前小区的ECGI和TAI发送给MME。
因为UE和MME之间的NAS安全上下文已经建立起来了，TAU request消息是使用NAS完成性保护秘钥完保的。如果MME对接收到的TAUrequest消息完成性保护检查失败，MME执行用户鉴权和NAS安全建立过程。在这个文档中，我们仅仅讨论MME成功完成完整性检查。

**2）【MME】TA 更新：分配新TA**
因为UE移动到没有注册过的TA上，MME选择一组TA分配给UE，并为UE配置一个新的TAI列表。这是，也要分配一个新的GUTI。

**3）【UE<-MME】TAU accept**
MME发送新选择的TA包含在TAU accept消息中(GUTI, TAI List)。这个消息是加密和完成性保护的，通过downlink NAS transfer消息从MME到eNB传输，接着通过DL information transfer消息从eNB传输给UE。

**4）【UE->MME】TAU complete**
如果分配了一个新的GUTI，UE给MME发送TAU complete消息来确认收到了新的GUTI。

一旦TAU过程完成，UE现在具有了一个新的TAI列表，并通过eNB5获得服务。

# III. EMM case 9. cell reselection with TAU

图2显示了idle模式下的UE移动到一个没有注册的TA时需要的小区重选过程。在图中，UE驻留在eNB2的cell5,移动到eNB5的cell13。

![这里写图片描述](http://img.blog.csdn.net/20151010123621479)

**cell reselection**
**1）~7）【UE】小区重选**
UE移动eNB5触发小区重选。UE测量邻区并排序，基于结果，选择满足消息重选准则的eNB5 cell13作为最合适的小区。这里的小区重选过程和之前文档介绍的一样。

**TAU**
当选择了eNB5 cell13，UE知道这个小区不属于之前分配TA中。所以，UE一旦完成重选过程就执行TAU过程。TAU过程和之前的描述的EMM case-5一样。不同之处在于谁发起的TAU过程。对于周期性TAU过程，UE因为TAU定时器T3412的超时执行TAU给网络报告当前位置。然而，对于上面给到的TAU，则是因为UE移动到一个没有注册的TA时UE执行TAU。所以这是TAU request消息的TA更新类型是TA updating而不是periodic updating。TAU过程简要叙述如下：

**8）【UE->MME】TAU request**
UE通过发送TAU request消息(Update Type=TA Updating, GUTI, Last Visited TAI, KSIASME, NAS-MAC)给MME来请求TA更新。这个TAU request消息和第二章包含相同的信息。这个消息也是使用NAS完整性秘钥完保的。UE和eNB建立RRC连接，并发送这个消息，接着从idle 状态转到connected状态。

**9）【MME】TA update：分配新的TA**
一旦通过uplink NAS transfer消息接收到TAU request消息，MME从uplink NAS transfer消息中的TAI标记处UE当前驻留的哪个TA，并在TAU request消息包含的last visited TAI知道上次报告的TA。
因为UE当期驻留的TA不是之前MME分配的TA，MME允许新分配一个TA集合最适合UE当前位置和速度。对于快速移动的UE，分配大范围的TA来减少由TAU引起的信令负载。当时，这样，由寻呼引起的心灵负载就会增加。寻呼消息将需要更长时间。所以为了每一个UE分配不同TAI，在TAU信令和寻呼性能之间的权衡应该考虑。MME更新last visited TAI包含在和UE当前TAI的UE上下文中。

**10）【MME，SGW，PGW，PCRF】EPS承载/会话修改**
一旦UE位置改变，MME通过发送modify bearer request消息通过SGW这个变化。是否需要MME给PCRF报告TA的变化，包含UE初始附着建立EPS会话时从PCRF接收到的change report action参数中。会话修改构成执行，报告给PCRF TA的改变。

**11）【UE<-MME】TAU accept**
MME通过tau accept消息给UE发送新的TAI列表。这是，这个消息和第二章包含相同的信息。并且也是完整性保护和加密的。

**12）~14）【UE，eNB，MME】转移到idle状态**
在TAU过程之后，MME释放S1连接，接着释放eNB5和MME之间的S1信令连接、eNB5释放和UE之间的RRc连接。现在UE和MME之间的ECM连接都移除了。并返回到idle摸下。

**15）【UE】驻留在cell13**
一旦返回到idle状态，UE驻留在cell13，现在，在每一个DRx周期结尾处UE醒来测量cell13的信号。

# IV. EPS实体信息
handover with TAU过程前后存储在EPS实体中的信息和handover without TAU的一样。只有TAI的信息由于TA更新发生了变化。
cell reselection with TAU过程前后存储在EPS实体中的信息和cell reselection without TAU一样。只有TAI的信息由于TA更新发生了变化。

# V. closing

我们到目前为止讨论了UE移动到一个在注册TA中的切换和小区重选过程。我们学习到通过切换或者小区重选过程，UE通过TAU过程获得了新的TAI列表。
下面的文档将讨论UE移动到另外一个城市，包含从城市1detach，在城市2 attach。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “LTE EMM Procedure 6. Handover without TAU – Part 1. Overview ofHandover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6224
[3] Netmanias Technical Document, “LTE EMM Procedure 6. Handover without TAU – Part 2. X2Handover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6257
[4] Netmanias Technical Document, LTE EMM Procedure 6. Handover without TAU – Part 3. S1Handover”, April 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6286
[5] Netmanias Technical Document, “LTE EMM Procedure 7. Cell Reselection without TAU”, April 2014,http://www.netmanias.com/en/?m=view&id=techdocs&no=6322
[6] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 2. Call Flow of InitialAttach” , January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[7] Netmanias Technical Document, “LTE EMM Procedure 5. Periodic TAU”, February 2014,http://www.netmanias.com/en/?m=view&id=techdocs&no=6193
[8] Netmanias Technical Document, “LTE EMM Procedure 3. S1 Release”, January 2014,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6110
[9] Netmanias Technical Document, “LTE EMM Procedure 10 &11. Move to Another City and Attach”,May 2014, TBD
[10] 3GPP TS 23.401, “General Packet Radio Service (GPRS) enhancements for Evolved UniversalTerrestrial Radio Access Network (E-UTRAN) access”
[11] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010