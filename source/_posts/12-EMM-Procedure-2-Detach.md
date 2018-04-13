---
title: 12-EMM Procedure 2. Detach
date: 2016-04-03 22:19:31
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---

原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6108


----------
# I. Introduction

这篇文档讨论EMM case-2。在这个过程中，用户从附着的网络上去附着。用户在EMM case-1通过初始附着附着到网络上在emm-registered状态使用LTE服务，在使用服务后，当在ECM/RRC-connected状态或者ECM/RRC-idle状态下用户可能被网络或者UE去附着。无论任何一种情况，一旦去附着过程完毕，用户的EPS承载释放并且状态信息清除。

这篇文档介绍LTE网络的detach过程，组织如下：第二章根据detach是谁发起的来定义不同的类型，第三章描述每一个detach类型的具体过程，第四章深入了解在detach后每一个EPS实体的信息有如何变化。

# II. Cases of Detach
通过初始附着生成EPS会话和默认EPS承载之后用户可以使用LTE服务。在一些情况下，用户一旦使用完了服务可以may从网络detach。在另外一些情况下，用户一直在使用网络服务时被网络detach，然后不能和网络保持连接了。

一旦用户从网络detach，所有分配给这个用户的EPS会话和承载都将被释放。释放过程将删除用户的MM上下文和给EPS实体设置的EPS承载。这时，EMM状态从registered转到de-registered。如果用户正确的detach了，GUTI，NAS层的用户ID和安全上下文，用户接入网络所需要的这些信息都将在UE和MME中保持有效，所以用户可以在下一次接入网络时使用这些信息。

detach可以由UE和网络来触发。网络触发的detach是由MME或者HSS引起的。
detach根据在哪里触发可以分为以下几类：
**1) Detach Case 1: UE发起的Detach**
UE可以发起detach:

 - 如果UE关机
 - USIM从UE中移除
 - UE试图使用non-EPS服务（比如，CSFB，SMS等等）

**2) Detach Case 2: MME发起的Detach**
MME发起detach可以更信息分为显式detach和隐式detach。
对于显式detach，MME通过发送detach request消息提前通知UE它要detach，并通知UE在detach之后是否要重新attach。
对于隐式detach，MME不通知UE就发起detach过程，因为UE没有能力和MME通信。
MME可以发起
 i)显式detach
 

 - 运营商O&M（操作或者维护）的目的
 - 重新鉴权失败
 - 如果MME不能为用户提供分配资源

ii）隐式detach

 - 由于无线链路质量比较差导致不能和用户通信（比如RLF）

**3) Detach Case 3: HSS发起的Detach**
HSS可以发起detach:

 - 如果HSS提供的用户配置文件发生变化，保存在MME中的配置文件也不得不改变时
 - 如果运营商试图限制一个非法的UE接入网络

在下面几章中，我们将描述下面三种类型的详细过程。在这个三个类型中，假设用户在detach之前处于emm-registered ecm-connected rrc-connected状态，并且仅仅通过默认EPS承载提供服务。图1描述了建立了哪些连接，在detach前后用户和控制面的UE和MME都处于什么状态。在detach之前，已经建立默认EPS承载和相关的控制连接，用户处于emm-registered ecm-connected rrc-connected状态。在detach之后，默认EPS承载和所有的信令连接都被释放，用户进入EMM-Deregistered, ECM-Idle and RRC-Idle状态。

![这里写图片描述](http://img.blog.csdn.net/20150922121632123)

# III. UE-initiated Detach

图2展示了用户发起的detach是怎么执行的。用户发起的detach是由UE触发的，由UE发送detach request消息。当UE接收到MME的detach accept消息（除非用户关机情况下）这个过程结束。

![这里写图片描述](http://img.blog.csdn.net/20150922121934689)

**❶ UE触发detach**
当检测到UE触发detach，UE和MME都能感知到，这两个实体开始以下的过程：
1）【UE->MME】detach request
UE通过发送detach request消息给MME，请求MME detach。detach request消息参数根据这个消息传送的方向有不同的解释。如果是从UE到MME，这个消息参数如下所示：
Detach Request (GUTI, KSIASME, Detach Type(Switch Off))

 - GUTI：MME在网络attach时分配的用户ID
 - KSI-ASME：UE使用的KSI值
 - detach type：只是detach的类型
     -  switch off：只是detach是normal detach（0）还是switch off（1）
     - type of detach：EPS detach

2）【UE】处理安全和承载上下文
在发送完detach request消息后，UE存储它当前NAS安全上下文，GUTI和TA信息，并删除EPS承载上下文。

3）【MME】通知detach意图和处理安全上下文
在接收到detach request消息后，MME可以知道UE detach的意图。并存储用户当前NAS安全上下文，检查detach的类型，例如，是否是normal detach或者关机。通过上述步骤，MME可以知道是否需要发送detach accept消息。

**❷ EPS会话终止**
一旦MME感知到UE发起的detach并存储用户当前NAS安全上下文，请求对已经激活EPS会话的终止。这个请求触发PCEF（PGW）发起EPS终止，释放所有分配给用户的网络/无线资源。

**1）EPS承载释放和PCC策略移除**
4）【MME->SGW】请求EPS会话释放
MME和SGW在S11接口上通过GTP-C协议通信。MME通过发送给SGW一个delete session request消息来请求删除用户的会话和默认EPS承载。这时，在消息中传递 默认EPS承载ID和UE位置信息 (ECGI, TAI)。

5）【MME】删除EPS承载上下文
MME在发送delete session request消息后删除用户EPS承载上下文。

6）【SGW->PGW】请求EPS会话释放
SGW和PGW在S5接口上通过GTP协议相互通信。SGW转发从MME接收到的delete session request消息给PGW。

7）【SGW】删除EPS承载上下文
SGW在发送delete session request消息后删除用户EPS承载上下文。

8）【PGW->PCRF】通知EPS会话终止
PGW和PCRF在Gx接口上通过Diameter协议通信。PGW发送给PCRF一个CCR（CC-request）消息来通知PCRF用户已经结束了使用服务。这样，它就可以发起EPS会话终止过程。

9）【PCRF】删除PCC策略
一旦PCRF从PGW收到CCR消息，PCRF删除用户的PCC策略。

10）【PGW<-PCEF】确认EPS会话终止
PCRF通过发送给PGW CCA（CC-answer）消息来确认用户的PCC策略已经删除了。

11）【SGW<-PGW】响应EPS delete session request
当PGW从PCRF接收到CCA消息，PGW发送给SGW一个Delete Session Response消息作为对delete session request消息的响应。

12）【PGW】删除EPS承载上下文
在发送delete session response消息后，PGW删除用户的EPS承载上下文。

13）【MME<-SGW】响应EPS Session Release Request
当SGW从PGW接收到delete session response消息后，SGW发送给MME delete session response消息作为对delete session request消息的响应。

14）【MME<-UE】确认detach
一旦接受到delete session response消息，MME确认用户资源释放已经由PCRF同意了。接着MME发送给UE detach accept接收到作为detach request消息的响应。detach accept消息仅仅当UE的detach请求原因除关机之外的情况下才发送。如果detach 请求原因是因为设备关机，么么不会发送detach accept消息。

**(2) S1信令连接释放**
一旦发送完detach accept 消息给UE之后，么么和eNB释放分配给用户的所有资源（S1信令连接，RRC连接和eNB中的UE上下文），以为UE不要他们的服务了。

15）【eNB<-MME】确认S1信令连接释放
MME发送UE Context Release Command给eNB来释放S1信令连接。

16）【UE<-】RRC连接释放
eNB发送RRC Connection Release消息给UE来释放任何没有释放的RRC连接。

17）【eNB】删除UE上下文
eNB删除所有和UE相关的信息。

18）【eNB->MME】RRC Connection Release Complete
最后eNB发送给MME UE Context Release Complete消息作为对15）请求消息的响应。

# IV. MME-initiated Detach

图3显示了MME发起的detach是怎么执行的。MME发起的detach是由MME触发，接着MME发送detach request消息给UE，当分配UE的EPS会话资源都被释放后这个过程结束。

![这里写图片描述](http://img.blog.csdn.net/20150922122055404)

**❶ MME触发的detach**
下面描述了MME检测到detach触发之后，在EPS会话终止过程执行之前的步骤。如果用户这时处于idle状态，MME执行寻呼来建立S1信令连接。
1）【UE->MME】detach request
因为这是一个显式detach，MME发送detach request消息来请求UE detach。消息参数包含如下：
Detach Request (Detach Type(Re-attach required or not), Cause)

 - detach type：指示UE在detach之后是否需要重新attach（001-需要重新attach，010-不需要重新attach）
 - cause：指示detach的原因
 - 对于隐式detach，MME不发送detach request消息给UE。

2）【MME】处理安全上下文
在发送detach request消息给UE之后，MME在删除EPS会话之前存储当前NAS安全上下文。下次重新attach时，MME可以使用这些存储的上下文，并跳过鉴权和ＮＡＳ安全建立过程。

3）【UE】通知detach意图和处理安全承载上下文
在接收到MME发送的detach request后，UE获知MME detach的意图。UE检查detach的类型是否需要在detach之后重新attach。然后存储当前NAS安全上下文，并删除EPS承载上下文。

**❷ EPS会话终止**
一旦MME存储NAS安全上下文，MME请求PGW终止用户的EPS会话。这个请求触发PCEF（PGW）发起EPS终止过程，释放分配给用户的所有网络无线资源。
**（1）EPS承载释放和PCC规则移除**
通过4）~13），MME请求终止用户EPS会话，PCEF一旦接收到请求删除PCC规则，删除S5承载资源。在MME发起的detach中需要重新attach时，MME可以保存当前的UE-AMBR，在下次重新attach时UE可以更快的建立EPS承载。

14）【UE->MME】确认detach
一旦接收到detach request消息，在存储NAS安全上下文和删除EPS承载上下文之后，UE发送detach accept消息作为detach request像爱惜的响应。对于隐式detach的情况，1）14）16）步骤是跳过的。

（2）S1信令连接释放
在这节中，MME在接收到UE发送的detach accept消息和从SGW发送的delete session response消息后，释放所有未释放的资源。这节的过程和第三章的15-18步相同，除了detach 类型是允许重新attach，UE是在RRC连接释放完成后重新attach到网络的。

# V. HSS-initiated Detach
图4描述了HSS发起detach的过程

![这里写图片描述](http://img.blog.csdn.net/20150922122158199)

**❶ HSS触发的detach**
当因为签约信息退回由HSS触发detach，HSS试图立即删除用户的MM上下文和EPS承载。
1) 【MME<-HSS】 Detach Request
HSS和MME在S6a接口上通过Diameter协议相互通信。HSS通过发送请求给MME Cancel Location Request (CLR)消息来detach用户。使用以下参数：
Cancel Location Request (IMSI, Cancellation Type)

 - IMSI：要detach的用户ID
 - cancellation type = Subscription Withdrawn：指示detach的原因

**❷ EPS会话终止**
一旦从HSS接收到Cancel Location Request (CLR) 消息，MME释放之前分配给UE的所有资源。这个步骤和MME发起detach的过程是一样的，除了在2）中描述的额外的步骤。MME需要发送给HSS Cancel Location Answer消息作为Cancel Location Request消息的响应。

2) [MME -> HSS] 响应Detach Request
一旦从UE接收到detach accept消息，和从SGW接收到delete session response消息，MME会发送给HSS Cancel Location Answer消息作为Cancel Location Request消息的响应。


# VI. EPS Entity Information: Before/After Detach

这章描述了在执行“EMM Case 2: Detach”步骤之后EPS实体中的信息是如何变化的。每一个实体中的所有信息被分类为UE ID，UE Location，安全和EPS会话/承载信息。

##  6.1 Before Detach
在detach之前用户处于ECM/RRC-Connected状态。所以在detach之前，所有的EPS实体都有和初始附着完成后他们具有的信息相同。图5列出了在detach之前每一个实体中所有的信息。

![这里写图片描述](http://img.blog.csdn.net/20150922122259375)

##  6.2 After Detach
在detach之后，用于用户下次更快和安全attach的信息存储在UE和MME中。所有和用户相关的其他上下文信息，例如NAS安全上下文，GUTI，MME分配的TAI信息都将会释放。图6列出了在detach之后每一个EPS实体中的信息。在detach之后需要删除的信息标示为灰色，不要保留的信息标示为黑色，在下次attach时需要使用的信息被表示为蓝色。

![这里写图片描述](http://img.blog.csdn.net/20150922122350816)

# VII. Closing

我们已经学习了连接到LTE网络的用户从网络detach的过程。我们根据触发的位置分类detach类型，并对每一种类型进行详细的讲解。我们对比了detach前后每一个实体存储信息的变化。接下来的文档将讨论在用户不活动时删除S1资源的情况。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 2. Call Flow of Initial
Attach” , January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[3] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010
