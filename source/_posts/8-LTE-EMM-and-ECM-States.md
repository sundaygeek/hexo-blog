---
title: 8-LTE EMM and ECM States
date: 2016-04-03 22:18:43
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---

原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=5909


# I. Introduction

通过之前的技术文档，我们已经学习了LTE网络架构和LTE identification和LTE安全操作（当用户附着到LTE网络时需要的，并保证通信的安全）。现在我们开始更深入学习LTE的详细过程。

一旦用户附着到LTE网络，i）在网络端鉴权用户和注册用户，ii）EPS会话和承载建立用于提供服务，iii）触发支持用户移动的移动性管理的功能。在这个时候，MME通过和用户建立信令连接并交换控制信息来负责这三个任务。
在用户和网络之间的移动性和会话管理是由UE和MME的控制面的NAS层的NAS协议来控制的。这两个实体使用NAS消息相互通信。NAS协议定义在3GPP 24.301中。NAS可以大致分为EPS移动性管理EMM和EPS会话管理ESM。通过一系列的技术文档，我们描述EMM和ESM这两个过程。当前这个文档是这个系列文档的第一个，将描述i）EMM状态，ii）一旦EMM过程发起，在EPS实体中会保存什么用户信息。

本篇文档组织如下：第二章介绍EMM过程的类型和EMM相关的状态。第三章描述这些状态之间的转换。第四章总结在EPS实体中EMM的特征。第五章讨论在EMM/ECM/RRC状态的不同组合下EPS实体中保存着什么样的用户信息。


# II. EMM, ECM and RRC States

## 2.1 EMM过程的类型

表1列出了NAS协议支持的EMM过程的类型和属于这些类型的特定的EMM过程。详细的EMM过程将通过这个文档在下面解释。我先简要看看每一个EMM类型都包含哪些特定的过程。

![这里写图片描述](http://img.blog.csdn.net/20150913171120523)

有三张类型的EMM过程：
i）EMM 一般过程：是指在UE和MME之间NAS信令连接存在时总是可以发起的过程。这类过程分为五种过程：GUTI allocation（GUTI分配）Authentication（鉴权）identification（身份认证）Security mode control（安全模式控制SMC）EMM information（EMM消息）
ii）EMM特殊过程：是指和用户移动性相关的过程（注册，位置更新）。可以分为三种过程，attach（附着）detach（去附着）TAU（TA更新）。在和3gpp网络共存的LTE网络中，额外的过程也包含在这类中（比如，combined attach联合附着，combined detach联合去附着，combined TAU联合TA更新）。但是因为这个文档仅仅讨论LTE网络，所以这几种没有列出在表1中。
iii）EMM连接管理过程：是指和NAS信令连接建立有关的过程。可以分为三种过程，service Request（服务请求）paging（寻呼）NAS消息的传输。

## 2.2 EMM/ECM/RRC状态

EMM是NAS层的一个子层。随着EMM过程的进行，UE处于七种EMM状态之一，MME处于四种EMM状态之一。在这些状态中，一些状态例如“EMM-registered”和“EMM-Dergistered”是这两个实体都具有的共同状态，在下文中，EMM状态就是指这两种状态。

为了UE和MME之间相互交换NAS消息，在他们之间必须建立信令连接。这个连接叫EPS连接管理（ECM）连接。它包含UE与eNB之间的RRC连接和eNB与MME之间的S1信令连接的逻辑连接，如图1所示。也就是意味着，当ECM建立和终止时，RRC和S1信令连接全部建立和终止。对于UE，一个建立的ECM连接意味着与eNB之间具有RRC连接，对MME，意味着与eNB之间具有S1信令连接。

图1描述了和UE与MME有关的EMM，ECM，RRC状态。EMM可以是“EMM-Registered” or “EMM-Deregistered”状态取决于附着到网络还是去附着到网络。ECM是可以是“ECM-Connected” or “ECM-Idle”取决于NAS信令连接是否建立。同样的，RRC可以是“RRCConnected” or “RRC-Idle”状态取决于是否有RRC连接建立。表2显示了什么条件下UE待在什么状态。

![这里写图片描述](http://img.blog.csdn.net/20150913173806375)
![这里写图片描述](http://img.blog.csdn.net/20150913173813863)

# III. EMM State Transition

EMM，ECM和RRC状态随着EMM过程的处理发生改变。因为RRC连接是ECM连接的一部分，所以在UE的角度看ECM和RRC总是保持相同的状态。在图2中描述了状态转换的过程以及触发状态转换的事件。在图2中用户EMM，ECM/RRC连接的组合用A B C D表示。用户的过程将会是这样组合之一，见表3。

![这里写图片描述](http://img.blog.csdn.net/20150913174628407)
![这里写图片描述](http://img.blog.csdn.net/20150913174635978)

## 3.1 EMM-Deregistered状态

在A和B中，状态都是EMM-Deregistered，ECM-idle，RRC-idle，UE从网络去附着了。但是，在这两种情况下在网络端存储的手机信息是不一样的。在A情况中，在网络端处理提供的信息，没有包含任何UE信息。而在B情况下，网络保存着UE上次附着时网络获得的GUTI和NAS安全上下文。根据在状态A和B中，UE是否附着到网络，初始附着过程可能会有变化。

在状态B，在UE再次附着到网络时，网络保持着鉴权和安全建立所需求的信息，过了一段时间之后，网络会删除这些信息，并转换到状态A。

在EMM-Deregistered状态（A或者B），当选择PLMN和小区的时候，UE能分辨出来UE能通信的哪个网络和哪个小区。当UE请求接入网络获取服务的允许时，开始初始附着过程，然后UE状态转换到C（EMM-registered，ECM-connected，RRC-connected）。

## 3.2 EMM-Registered状态

在C和D状态，状态是EMM-registered，UE接入或注册到网络。但是ECM和RRC的状态是ECM-connected/RRC-connected或者是ECM-idle/RRC-idle状态取决于UE的激活特性。一旦UE成功地附着到网络，状态从EMM-Deregistered（A or B）转换到状态C，UE在使用服务的时候将会一直呆在状态C，当不用服务时转换到状态D。

在状态C，在控制面无线和网路资源分配给信令连接，在用户面分配给EPS承载。当有一个比当前正在通信服务的小区更好无线信号质量的小区时UE可能执行切换到邻小区。但是，当在状态D，UE是去激活的，ECM/RRC连接被释放了。资源在控制面没有分配给ECM连接，也没有分配给EPS承载（DRB和S1承载），除了S5承载一直存在。在这个状态，没有发送到UE或者网络的上下行流量。为了在状态D能传输用户流量，就需要建立ECM连接，转换到状态C，需要建立新的DRB和S1承载来激活EPS承载。在状态D，UE通过测量服务小区和邻小区的无线信号强度根据小区重选准则来选择一个小区驻留（见文档LTE EMM Procedure: 7. Cell Reselection without TAU）。

当i）上下行有新流量，ii）UE在idle状态由于TA改变或者TAU定时器超时，需要进行TAU请求时UE从状态D转换到状态C。另一方面，当i）UE去激活（也就说在一段时间内没有上下行UE流量）ii）UE在状态D完成TAU之后释放资源时UE状态从C转换到D。

当在EMM-Registered（C或D），如果UE从网络去附着，如果UE是关机或者无线链路失败RLF（如果在无线链路上数据包错误率超过门限值），这种情况下，UE转换到B转台（EMM-deregistered）。另外，当UE在状态C执行切换到非LTE网络，或者UE请求附着到网络被拒绝，或者TAU更新请求被拒绝，这时候，UE状态从EMM-registered转换到EMM-deregistered。

# IV. EMM Features

在这章，我们将讨论在图2的各个状态由EPS实体支持的EMM过程相关的特性。下面，我们将讨论以下议题：i）在每一个状态，被EPS实体知道的UE位置信息的粒度，ii）在什么状态EPS承载和NAS信令连接建立，iii）在每一个状态和UE移动性相关的特性，iv）在每一个状态在EPS实体中都设置了什么类型的ID。

## 4.1 UE位置信息

在表4中展示了被每一个EPS实体识别的UE位置信息的粒度。EMM-registered状态标示UE是附着到网络且网络知道UE的当前位置。在状态C，网络在小区的粒度上知道UE的位置，在状态D，网络在TA粒度上知道UE的位置。对于HSS，不像其他EPS实体，除了UE在状态A，是在MME粒度上知道UE的位置信息。

![这里写图片描述](http://img.blog.csdn.net/20150915104656575)

## 4.2 EPS承载和NAS信令连接
表5展示了在什么状态传输用户流量的EPS承载和传输NAS信令消息的NAS信令连接建立的。一旦UE成功附着到网络，变成EMM-registered状态，UE将通过EPS承载使用服务。一个EPS承载包含三个承载：DRB承载，S1承载和S5承载。如图3所示，当传输用户流量在ECM-connected/RRC-connected状态下，所有的这三种承载是建立和保持激活的，另一方面，当用户没有数据流量在ECM-idle/RRC-idle状态时，只有S5承载是建立，其他两种承载是去激活的。

NAS信令连接包含RRC连接和S1信令连接，是在当传输用户流量时建立的。当用户从网络去附着，或者附着在网络上但是在idle状态下，这时候ECM连接时释放的。

![这里写图片描述](http://img.blog.csdn.net/20150915105401396)

![这里写图片描述](http://img.blog.csdn.net/20150915105502558)

## 4.3 移动性

表6列出了在每一个状态UE移动性相关的特性。
移动性：
在EMM-deregistered开机的UE通过选择PLMN和小区来知道需要驻留在哪个小区和哪个网络。当有一个无线信号质量比当前小区好的小区时，在EMM-registered状态使用服务的UE从当前小区切换到另外邻区。对一个当前不使用服务的UE，当有一个无线信号质量比当前小区好的小区时，在EMM-registered状态的UE从当前小区重选到另外邻区。

TAU：
在EMM-registered状态下的UE，不论是否使用服务，当TA改变时就需要更新TA。但是当在ECM-idle/RRC-idle状态下，即使TA没有改变，当TAU定时器超时时也会更新TA。TA更新时由UE发送TAU request消息来发起的。
当UE在ECM-idle/RRC-idle状态，ECM/RRC应该首先建立，然后UE需要转移到ECM-connected/RRC-connected状态来更新TA。一旦UE在状态C发送TAU request消息并从MME接收到TAU accept消息，TAU过程就结束了。这时候，释放ECM/RRC连接，然后UE返回到ECM-idle/rrc-idle状态。

寻呼：
当UE附着到网络，但是在idle状态，如果有用户流量传输，网络需要发起寻呼来唤醒UE，接下来UE状态转换到C。寻呼是根据在最后一次TA更新中由UE提供的TAI信息来进行的。
![这里写图片描述](http://img.blog.csdn.net/20150915110925007)

## 4.4 UE ID

表7列出了每一个实体在四种状态中所具有的UE ID。UE IP地址当UE初始附着到网络由PGW分配的，会建立默认承载，并且当默认承载去激活时释放。GUTI是当UE初始成功附着到网络由MME分配的，用来代替IMSI来使用。如果UE从网络成功的去附着，UE和MME会波爱吃UE最后一次的GUTI，并且在下次UE附着到网络时使用它，即使在附着之后。C-RNTI是由eNB分配，用来在一个小区中RRC-connected状态下区别UE的，只有当和小区中已经分配的C-RNTI关联才是有效的。eNB UE S1AP ID and MME UE S1AP ID是由eNB和MME来使用分辨在S1-MME接口上的UE的。Old eNB UE X2AP ID and New eNB UE X2AP ID是服务eNB和目标eNB用来在X2接口上分辨UE的（当UE在服务eNB和目标eNB之间执行切换时）。

![这里写图片描述](http://img.blog.csdn.net/20150915112929185)

# V. EMM User Information

这一章将描述在图2状态下EPS实体和用户关联的数据。

## 5.1 EMM-deregistered+ECM-idle/rrc-idle

表8描述了在状态A下EPS实体包含的用户信息。在状态A，除了运营商提供的信息外，网络没有用户信息。也就是说，在状态A，在UE和NE中所有的信息都是运营商提供的。IMSI，LTE K，主秘钥在用户鉴权是需要，默认APN，EPS QoS签约信息和接入配置在建立EPS会话和默认EPS承载时都需要。在EPSQoS签约配置文件和接入配置包含如下信息：

 - EPS QoS签约配置文件（在HSS中）：QCI，APR，APN-AMBR和UE-AMBR
 - 接入配置文件（在SPR中）：SDF过滤器，QCI，APR，APN-AMBR、UE-AMBR和计费方法。

![这里写图片描述](http://img.blog.csdn.net/20150915113836009)

## 5.2 EMM-deregistered+ECM-idle/rrc-idle

表9描述了在状态B下EPS实体包含的用户信息。在状态B，最后一次UE附着网络使用的信息在UE和MME中保持有效，所以，在下次附着时UE可以使用相同的信息。这些信息包括由MME最后一次分配的GUTI，在UE最后一次更新TA时的TA信息，在UE和MME之间最后一次使用的NAS安全上下文，由MME分配给UE的UE-AMBR等等。在状态A中UE和NE中没有的用户信息，在状态B中包含的被标记为绿色，已经在状态A分配的是黑色的。如表9所示。

![这里写图片描述](http://img.blog.csdn.net/20150915121453870)

## 5.3 EMM-registered+ECM-connected+RRC-connected

表10列出了在状态C下EPS实体所拥有的用户信息。在状态C，建立了EPS承载和NAS信令连接，执行切换和TA更新。所以，在这个状态设置了这些过程需要的所有用户信息。为了容易和状态B进行区分，在状态C中新加入的标记被蓝色，B状态的标记为绿色。

![这里写图片描述](http://img.blog.csdn.net/20150915121851776)

## 5.4 EMM-registered+ECM-idle/rrc-idle

表11列出了在状态D中EPS实体拥有的用户信息。这个状态首先可以从A/B转移到C。在状态D，在状态C分配给EPS承载和NAS信令连接的所有资源，除了S5承载，都被释放了。这些释放的信息被标记成灰色。在S1承载上，下行资源被释放，但由PGW分配的上行资源被保持。所以，在下次转换到状态C时，在建立S1承载时，相同的上行承载将会使用。我们可以看到一旦UE从状态C变成idle状态，在UE和eNB之间的所有的资源都被释放。

![这里写图片描述](http://img.blog.csdn.net/20150915122523502)


# VI. Closing

我们学习了EMM，ECM，RRC状态。在ESM过程中讨论EPS会话和承载建立相关的更有意义。作为EMM过程的一部分，UE初始附着流程包含默认EPS承载的建立，ESM相关的相关的EMM过程也包含在这个文档中。基于我们对EMM，ECM，RRC状态的讨论，我们为更深入的学习EMM过程。

# References
[1] 3GPP TS 24.301, “Non-Access-Stratum (NAS) protocol for Evolved Packet System (EPS); Stage 3”.
[2] Netmanias Technical Document, “LTE Network Architecture: Basic”, July 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5904
[3] Netmanias Technical Document, “LTE Security II: NAS and AS Security”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5903
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.
