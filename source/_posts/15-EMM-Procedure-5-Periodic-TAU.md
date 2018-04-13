---
title: 15-EMM Procedure 5. Periodic TAU
date: 2016-04-03 22:34:32
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6193

# I. Introduction

这篇文档描述EMM case-5 周期性TAU过程。这个过程是在ecm-idle rrc-idle状态下的UE因为没有数据传输而需要周期性的更新MME的位置信息。
当UE进入一个不在TAI列表中的TA时发起TAU，或者当TAU定时器超时。这篇文档介绍的是周期性TAU。当TAU定时器超时时，idle状态的UE发送TAU request消息报告自己的位置信息。在转移到ecm-connected rrc-connected状态后，执行周期性TAU过程，然后UE返回到idle状态。
这篇文档介绍idle状态的UE执行周期性TAU过程，第二章介绍周期性TAU的概念，第三章描述具体的过程，最后第四章总结在这个过程前后EPS实体信息的变化。

# II. Concept of Periodic TAU

在connected状态的UE已经建立UE到PGW的端到端的EPS承载。MME保持UE位置的跟踪。所以如果有到达UE的数据，网络可以立即传送。
但是如果UE在idle状态，UE和MME之间的信令连接和承载已经被释放，MME丢失了UE位置的跟踪。网络应该时刻知道UE的位置信息，无论是idle还是active，这是为了能够传送给UE数据。所以在idle状态的UE即使没有数据传输时，应该给MME周期性的报告自己的位置信息（比如UE所在的TA）。TA是一些小区的组合，由MME管理。idle状态的UE位置信息的识别是在TA级别。当UE初始附着网络时，MME会在attach accept消息中包含TAI列表和TAU定时器（T3412）给UE。使用这些信息，UE可以在TAU定时器超时时执行TAU过程。
当MME接收到UE的位置信息，MME会更新UE的最新位置信息。如果UE在idle状态下有数据到达UE，MME可以通过发送给这个TA下的所有小区寻呼信息来通知UE有数据到达，这个TA是idle状态的UE报告给MME的TA。图1描述了TAU执行的实例。UE1在初始附着时attach上了eNB1的cell2，并且通过attach accept由MME分配了TAI列表(e.g. TAI={TAI1, TAI2})和TAU timer (e.g.T3412=60 mins)，接着在转移到idle状态，❶ -> ❷ -> ❸ -> ❹。我们假设i)UE只在TAI列表中的TAs中移动，ii）UE报告TA信息的MME是存储着UE的上下文信息，iii）MME和UE都包含有效的NAS安全上下文。

![这里写图片描述](http://img.blog.csdn.net/20150924131840678)

在cell2 attach和转移到idle状态后，在t1时刻当TAU超时时UE1醒来并建立ECM信令连接。接着UE1发送给MME TAUrequest消息 (TAI=TAI1, Last Visited TAI=TAI1) 包含当前小区的TAI和上次访问的TAI(❶)。接着UE1接收到TAU accept消息后返回到idle状态。在接收到TAU request消息后，MME检查UE的上次TAI是否改变，如果变化了更新最新的TAI信息。如果有到达UE1的数据，SGW通知MME有下行数据，MME根据上次TAU报告的最新的TAI查找UE在哪个TA，并在这个TA下的所有小区发送寻呼消息。接下来，如果UE1移动到cell4，并且TAU定时器超时，UE给MME发送TAU request消息(TAI=TAI1, Last Visited TAI=TAI1)，因为UE一直在TA1中。如果UE1移动到cell11中，并且定时器超时，UE给MME发送TAU Request 消息(TAI=TAI2, Last Visited TAI=TAI1)，用TAI2代替TAI1，因为这个小区属于TAI2。接着MME更新UE1的上次TAI为TAI2。图2描述了连接建立，TAU前后用户面和控制面UE、MME的状态。

![这里写图片描述](http://img.blog.csdn.net/20150924131923513)

**UE状态如下**：
（i）在TAU之前，UE在EMM-Registered, ECM-Idle and RRC-Idle状态，E-UTRAN分配的资源，ECM信令连接都释放了。

（ii）在TAU过程中，UE处于EMM-Registered, ECM-Connected and RRC-Connected状态。周期性TAU过程不同于初始附着和service request过程，周期性TAU过程没有e-rab建立，只有ecm信令连接用于传输周期性TAU相关的NAS消息。

（iii）在TAU过程之后，ECM连接释放，释放E-UTRAN资源。接着UE返回到EMM-Registered, ECM-Idle and RRC-Idle状态。

图3描述了在TAU前后状态转换的过程。一旦T3412超时，idle状态的UE给MME发送TAU request报告当前TA和上次TA。MME在更新完最新UE位置信息后发送TAU accept消息。最后释放信令连接返回到idle状态。

![这里写图片描述](http://img.blog.csdn.net/20150924132015001)


# III. Procedure of Periodic TAU

图4和图5描述了周期性TAU的详细过程。图4中，UE的NAS通过发送TAU request消息给MME报告新的TA，更新类型标记为“Periodic Updating”。“Periodic Updating”类型的TAU不需要承载的建立，只需要建立ECM信令连接后就可以发送TAU request消息。当UE接收到TAU accept消息，释放ECM信令连接。TAU request消息是完整性保护。所以MME通过对这个消息完整性检查来决定是否执行鉴权过程。

![这里写图片描述](http://img.blog.csdn.net/20150924132102044)

**❶  Periodic TAU Triggering**

1) [UE] TAU Timer Expiry
idle状态UE在T3412TAU定时器超时后出发TAU过程给MME报告自己的位置信息。

**❷  ECM Connection Establishment and TA Report**

UE的NAS层 i）配置TAU消息，ii）发送RRC参数给RRC层。

2), 3) [UE – eNB] RRC Connection Establishment
一旦接收到TAU request消息，UE的RRC层给eNB发送RRC connection request消息，请求专用信令资源。eNB通过分配SRB信道和给UE发送RRC connection setup消息来建立RRC连接。

4), 5), 6) [UE -> MME] ECM Connection Establishment Request and TA Report
TAU request消息在UE到eNB之间包含在RRC Connection Setup Complete消息中，在eNB和MME之间包含在Initial UE Message消息中，TAU request消息是完整性保护和加密的。

The TAU Request消息如下信息:
TAU Request (Update Type=Periodic Updating, Active Flag=0, GUTI, Last Visited TAI, KSI ASME, NASMAC)

 - Update Type: 标识TAU类型，当TAU定时器超时设置为Periodic Updating
 - Active Flag: 标识是否有上行用户数据或者信令要传送，如果有这个参数设置为1，会在TAU之后建立承载和维持ECM连接。
 - GUTI: UE ID, 之前由MME分配，用来标识UE的。
 - Last Visited TAI: UE上次注册的TAI
 - KSIASME: index for KASME, the NAS security base key
 - NAS-MAC: TAU request的消息完整性保护时使用MAC

为了从NAS层接收TAU request消息，UE的RRC层在RRC Connection Setup Complete消息中包裹这个消息发送给eNB。这时，RRC Connection Setup Complete消息包含GUMMEI。这个ID是从NAS层接收的，标识UE注册在哪一个MME上。通常eNB可以连接不止一个运营商网络和MME，所以一旦接收到RRC Connection Setup Complete消息，eNB检查自己是否连接着这个标记的MME。接着，eNB把TAU request消息包裹在Initial UE Message消息中发送给MME。这时，eNB分配给initial UE message消息一个eNB S1AP UE ID标识。MME一旦接收到Initial UE Message消息，分配MME S1AP UE ID，并建立S1信令承载。这时候就完成了UE和MME之间的ECM信令连接，允许UE转移到connected状态。

**❸ UE Authentication and NAS Security Setup (Optional)**

7) [UE – MME – HSS] UE Authentication
MME一旦接收到TAU request消息，对这个消息进行完整性保护检查，如果检查通过，MME跳过UE鉴权过程，并继续使用自己保存的NAS安全上下文来传递NAS消息。如果检查失败了，需要执行UE鉴权。

8) [UE – MME] NAS Security Setup
一旦UE鉴权完成，NAS安全秘钥用于NAS消息传输。

![这里写图片描述](http://img.blog.csdn.net/20150924132148383)

**❹  TA Update**

9) [MME -> S-GW] TA Update
当MME接收到了TAU request消息后，重置TAU定时器，并给SGW发送Modify Bearer Request消息，转发UE的位置信息(ECGI, TAI)。

10) EPS Session Modification (Optional)
SGW接收到UE位置信息后，检查UE的小区是否发生变化。如果变化，SGW发送Modify Bearer Request消息给PGW通知这个变化。接着PGW通过EPS session modification过程发送同样的信息给PCRF。

11) [MME <- S-GW] Responding to TA Update Request
SGW给MME发送Modify Bearer Response消息作为对Modify Bearer Request 消息的响应。

12) [MME] Preparing TAU Accept Message
MME配置新的TAI列表来反映UE当前位置，或者分配一个新的GUTI（根据实现来决定）。

13) [UE <- MME] Sending TAU Accept Message
MME给UE发送TAU Accept 消息，完整性保护而且加密的。这个消息使用Downlink NAS Transport消息（从MME到eNB）和DL Information Transfer message消息（从eNB到UE）发送给UE。

14) [UE] Updating TIN and TAI List
当UE从MME接收到TAU Accept消息，检查GUTI和TAI列表值。如果这些值发生变化，UE会用新值来更新TIN (Temporary Identifier used in Next update)和TAI列表。这里的TIN是在下次UE发送TAU request消息时使用的用户ID，并且更新包含在TAU accept消息中的GUTI。

15) [UE] Acknowledging New GUTI
如果MME分配了一个新的GUTI，UE发送TAU complete消息给MME作为对新GUTI的响应。

**❺  ECM Connection Release**

16) [eNB <- MME] Requiring E-UTRAN to Release UE Context
在更新UE的位置信息后，MME给eNB发送UE Context Release Command消息来释放用于周期TAU的ECM连接，并释放存储在E-UTRAN中的UE上下文。

17) [UE <- eNB] Releasing RRC Connection
一旦接收到UE Context Release Command 消息，eNB释放UE上下文，并释放分配给UE的E-UTRAN资源。接着给UE发送RRC Connection Release消息来释放RRC连接，然后释放分配给UE的SRB。

18) [eNB -> MME] Announcing Release of UE Context from E-UTRAN
eNB给MME发送UE Context Release Complete消息指示S1信令连接已经释放了。现在，为TAU request建立的ECM连接释放了，UE重新回到idle状态。(ECM/RRC-Idle)

# IV. EPS Entity Information: Before/After Periodic TAU

这章描述周期性TAU过程前后EPS实体的信息。所有的信息被分类为UE ID，UE Location，安全，EPS会话/承载信息。


##  4.1 Before Periodic TAU

在TAU过程触发之前，UE在EMM-Registered, ECM-Idle and RRC-Idle状态，和service request发起之前存储的信息相同。即，E-utran分配的无线资源和相关的EPS承载和信令都被释放了。在图6中描述了在周期性TAU之前EPS实体中存储的信息。

![这里写图片描述](http://img.blog.csdn.net/20150924132239649)

##  4.2 After Periodic TAU

在周期性TAU止呕，ECM连接释放，UE重新返回到idle状态。存储的EPS实体中的信息和TAU request之前的信息相同，如图6所示。

# V. Closing

我们学习了周期性TAU过程。不像service request过程，这个过程不建立e-rab承载，但是需要建立ECM连接。在TAU完成后，ECM连接释放，UE重新返回到idle状态。如果有上行数据要发送，首先建立e-rab承载，然后在TAU之后发送数据，像service request过程一样。接下来将介绍connected UE移动到另外的TA引起handover，但是没有TAU的情况。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “LTE EMM Procedure 4. Service Request”, February 2014,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6134
[3] Netmanias Technical Document, “LTE Identification II: NE and Location Identifiers”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5906
[4] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 2. Call Flow of Initial
Attach”, January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[5] Netmanias Technical Document, “LTE Security I: LTE Security Concept and LTE Authentication”, July
2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=5902
[6] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010
