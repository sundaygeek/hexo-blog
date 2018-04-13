---
title: 22-LTE Policy and Charging Control (PCC)
date: 2016-04-03 22:36:00
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6562

# I. introduction

为了使用LTE服务，用户必须购买一个LTE设备，和运营商签署一个账户，并选择服务类型和想要的计划。为了支持和管理这些选择的服务，运营商根据用户的签约信息来配置签约文件。当用户使用LTE服务时，对每一个和不同服务相关的APN建立EPS会话。这时，网络决定一个策略：当EPS会话建立或者修改是,网络资源时怎么分配的，这些服务时怎么付费的。接着，当会话保持激活状态时，应用这个策略到EPS会话。这个过程叫做策略和付费控制PCC，主要由策略付费控制功能PCRF和策略付费增强功能PCEF实现。

当建立或者修改EPS会话，基于运营商的策略PCRF决定每一个服务数据流SDF的PCC规则。 PCEF (P-GW)检测一个SDF，并适用一个PCC规则，这个规则对用户数据包上的一个特定的SDF是特定的。它可以绑定SDF QoS和承载QoS，适用承载QoS到EPS承载。也就说，EPS承载上下文是在EPS实体(UE, eNB, S-GW, P-GW, and MME)上设置和修改的。

EPS会话的建立，修改和结束的执行时和PCC过程一致的。EPS会话修改过程可能包含EPS承载的建立修改和结束。在这个过程中，UE和MME之间的EPS承载上下文是按照NAS层的EPS会话管理功能ESM来处理的。

这篇文档讨论PCC规则和过程，是按照如下组织的：第二章定义PCC规则并描述PCC规则对于不同的服务烈性在GX接口时怎么提供的。第三章讨论伴随ESM过程的EPS会话建立和修改需要的PCC规则。第四章介绍当使用策略控制时，IP数据包是怎么处理的。第五章总结EPS实体中策略控制相关的信息。

# II.PCC rules

PCRF决定每一个SDF的PCC规则，并通过Gx接口把这些规则转发给PCEF（PGW）。PGW接着对每一个SDF执行这些PCC规则。在执行PCC规则后，当IP数据包到达，检测到每一个数据包属于的SDF，并根据他们的SDF对每一个数据包适用PCC规则。3GPP29.312按照如下定义PCC规则：

 - 目的：PCC规则的目的是检测一个数据包属于哪个SDF，并识别出SDF作用的服务，为这个SDF提供合适的付费参数，并为这个SDF提供策略控制。
 - 适用：适用于不同的SDF。PCEF (P-GW)通过使用数据包过滤器（SDF templates）来匹配对IP数据包进行分类，并对这些数据包应用上匹配的PCC规则。
 - 类型：动态PCC规则，或者预定义的PCC规则。动态PCC规则是在EPS会话建立或者修改时，由PCRF动态地提供给PGW的。预定义的PCC规则是在PGW中预先配置的，可以由PCRF激活或者去激活。
 - elements：PCC规则包含策略规则名字，service ID，SDF templates，gate status，QoS参数，付费参数等等，并根据运营商的策略而变化。

下面，我们详细介绍不同类型的PCC规则是怎样通过Gx接口传送的，并且在PGW上执行的。2,1节描述预定义的PCC规则，2.2节描述动态PCC规则。

##  2.1 预定义PCC规则

一些运营商适用限制不是他们服务的流量的比特率这样的策略，例如P2P。所以，我们使用P2P作为示例介绍下预定义PCC策略。图1描述了在PGW中预定义PCC策略是怎么配置的。

![这里写图片描述](http://img.blog.csdn.net/20151020095634078)

在这个图中，预定义规则“P2P”是在PGW中预配置和去激活的。因为这个规则是预定义的，一旦PCRF决定，就需要给PGW这个策略规则的名字 ie“P2P”，来代替发送这个规则的所有元素。一旦接收到P2P这个规则名字，PGW激活之前去激活的P2P规则，并执行。

##  2.2 动态PCC规则

动态PCC规则是在当有时间时使用的。图2描述了动态PCC规则在用户使用Internet和语音服务是在PGW中怎么配置的。图中做如下假设：

 - 运营商策略：互联网服务的接入性在一定程度上取决于网络资源。并且可以保证语音服务的数据速率。
 - APN：Internet和语音服务可以通过不同的APN接入。
 - PCC规则名字：Internet服务“Internet”，语音服务“voice-C”（SIP信令）/“voice-U”（voice packet）

![这里写图片描述](http://img.blog.csdn.net/20151020101401038)

“Internet”规则适用于接入Internet的所有数据包。它支持不限制的MBR（UL/DL），允许Internet接入最大可能的比特率。这个规则适用于接入Internet的默认承载。APN-AMBR(uL/DL)是设置为非限制的，如果没有其他用户在服务数据可以以最大速率传输。但是服务的用户越多，支持的速率越慢。

“voice-C”规则适用于SIP信令数据包。它适用于接入IMS网络的默认承载，支持100Kbps的APN-AMBR(uL/DL)。

“voice-U”规则适用于多媒体数据包，是用户语音数据包，支持GBR。在激活的语音会话中，分配的网络资源确保数据包随时是按照UL/DL 88 Kbps的速率交付的。专用承载建立，也是允许数据包以UL/DL 88 Kbps的速率交付。

每次建立或者修改一个EPS会话，PCRF动态决定PCC规则，包含根据选择服务类型特定规则的规则名字和规则参数。接着PCRF把这些参数提供给PGW。接着PGW在把这些参数映射到承载QoS参数上之后对IP数据包执行这些策略。

一旦用户通过初始附着过程接入Internet和IMS网络，用户默认承载保持连接直到UE从LTE网络中去附着。所以基本上，“Internet”“voice-C”规则在PGW中保持激活。另外一旦检测到语音电话，就在IMS APN上建立专用承载，并在通话结束时终止承载。

# 3. PCC procedures

我们再第二章讨论了PCC规则。在第三章，我们将通过EPS会话建立和修改过程在探索PCC规则。在LTE中，语音服务和Internet服务使用不同的PDN，所以他们有自己的默认承载。语音信令消息是使用SIP协议通过默认承载传输的，而媒体数据包（用户语音数据包）是使用RTP协议通过专用承载传输的。

Internet服务用户的初始附着和EPS会话建立过程已经在LTE初始附着的文档中讨论过了。我们深入讨论在EPS会话建立或者修改过程对用户接入IMS拨打语音通话时PCC是如何工作的。
为了在LTE网络拨打语音通话，附着LTE和IMS注册时提前必须的。一旦语音服务的EPS会话通过LTE attach过程建立了，attach到IMS网络的默认承载也在LTE网络中创建了。在这个过程中，voice-C语音服务信令的PCC规则适用于这个承载。通过这个承载，交付SIP信令消息，用户和IMS网络之间的IMS鉴权注册接着执行。
接着当有一个语音通话，IMS网络监测到它，并通过PCRF。PCRF决定PCC规则（“voice-U”）并转发至PGW。PGW为语音数据包传输创建专用承载，并且通过这个专用承载传输语音数据包。

##  3.1 语音会话建立：默认承载建立和IMS注册

图3描述了通过IMS APN初始attach的用户时如何建立EPS会话的。我们假设EPS会话建立是由用户打开UE而发起的。随着使用EMM消息完成初始附着过程，EMM之前我们已经介绍了，现在我们简要过一下ESM过程。

![这里写图片描述](http://img.blog.csdn.net/20151020105105973)

 ** 1）【UE->MME】PDN connectivity request ** 
UE通过PDN connectivity request消息给MME请求接入IMS网络。在ESM消息中的PCO协议配置选项域用来要求代理呼叫会话控制功能P-CSCF地址。这个消息是包含在attach request消息中，发送给MME。

 ** 2）【MME->SGW->PGW】请求语音会话的创建 ** 
MME在位置更新时从hss接收到签约配置文件中获得APN和签约QoS配置。从APN，MME意识到APN要为语音服务创建默认承载，并为这个默认承载分配ID。这个签约配置文件获得QoS参数(QCI = 5, ARP = 6, APN-AMBR = 100 Kbps)，这些配置文件应用于默认承载。MME准备create session request消息，包含默认承载ID，APN，PCO和签约QoS配置文件，并把它转发给PGW。这时候，从UE接收到PCO域被透传给PGW。

 ** 3）【PGW】UE IP和P-CSCF地址分配 ** 
PGW为IMS APN分配UE IP，并选择P-CSCF的地址。

 ** 4）【PGW->PCRF】EPS会话建立的通知 ** 
PGW通过发送Credit-Control-Request (CCR)消息把用户签约QoS配置转发给PCRF，请求鉴权。

 ** 5）~6）【PCRF，SPR】用户配置获取 ** 
PCRF可以从SPR中获得用户签约配置文件，并使用配置文件来为语音服务决定PCC策略。

 ** 7）【PCRF】策略决定 ** 
基于签约配置文件，PCRF决定EPS会话的策略。这个实例中是SIP信令，选择“voice-C”作为PCC规则。
“Voice-C” rule: QCI = 5, ARP = 6, APN-AMBR (UL/DL) = 100 Kbps, Charging Rule: Offline, SIP Packet Filter

 ** 8）【PGW<-PCRF】PCC规则提供 ** 
PCRF转发PCC规则(“Voice-C”) 给PGW。

 ** 9）【PGW】策略实施 ** 
一旦接收到(“Voice-C”) PCC策略，PGW执行策略参数，并把SDF QoS参数映射到默认承载QoS参数上。

 - P-GW (SDF): QCI = 5, ARP = 6, MBR (UL/DL) = 100 Kbps/100 Kbps, SDF Template (UL/DL) = (UE IP, * ,SIP, * , UDP) /( * , UE IP, SIP, * , UDP)
 - P-GW (Default Bearer): QCI = 5, ARP = 6, APN=AMBR (UL/DL) = 100 Kbps/100 Kbps, TFT (UL/DL) =
(UE IP, *, SIP, *, UDP)/( * , UE IP, SIP, *, UDP)
 ** 10）【MME<-SGW<-PGW】对语音会话创建的响应 ** 
作为对step-2请求的响应，PGW发送Create Session Response消息给MME。这个消息包含提供的QoS配置文件和上行策略参数，都需要转发给UE。
 ** 11）【UE<-MME】请求默认承载上下文激活 ** 
MME通过发送Activate Default EPS Bearer Context Request消息请求UE激活默认承载上下文。这个ESM消息包含APN，UE IP，P-CSCF地址，PGW提供的策略参数，是嵌入在attach accept消息中的。
 ** 12）【UE】策略执行：默认承载上下文激活 ** 
UE执行上行策略并激活默认承载上下文
UE: QCI = 5, APN-AMBR (UL) = 100 Kbps, TFT (UL) = (UE IP, *, SIP, *, UDP)
 ** 13）【UE->MME】默认承载上下文激活的通知 ** 
UE通过发送Activate Default EPS Bearer Context Accept消息通知MME，用于传输SIP信令消息的默认承载上下文已经激活了。
一旦SIP信令的默认承载建立完成，IMS鉴权和注册过程将紧接着执行。使用SIP协议的IMS过程在本文档的讨论范围之外，我们将会在volte文档中叙述。一旦IMS注册完成，UE准备发送/接收语音通话了。
##  3.2 语音会话修改：专用承载建立
一旦LTE附着和IMS注册完成，UE准备开始语音通话。但是，因为语音数据包需要实时传送，为SIP信令建立的默认承载提供的QoS不鞥呢满足语音数据包的QoS。所以，现在需要建立一个能盲足语音数据包QoS的新承载。对每一个语音通话，建立语音数据包的专用承载，通过语音会话修改过程。
图4展示了专用承载是怎么建立的，一旦检测到语音通话根据新的PCC策略语音会话时如何修改的。在途中，当发起端UE请求语音通话，PCRF从SIP信令（session progress消息）知道请求的是语音通话。接着PCRF决定一个PCC策略“Voice-U” rule (QCI = 1, ARP = 7, GBR/MBR = 88 Kbps)，并把它转发给PGW，接着PGW创建GBR类型的专用承载。当发起端UE通过默认承载接收到对端UE的200 OK的SIP消息时，UE通过专用承载转发语音数据包。
![这里写图片描述](http://img.blog.csdn.net/20151020134727905)
 ** 1）【UE->P-CSCF】语音通话生成 ** 
发起端UE通过发送invite消息给IMS网络请求语音通话。详细的过程将会在IMS网络的文档中叙述。
 ** 2）【PCRF<-P-CSCF】传送服务信息 ** 
一旦接收到这个SIP消息，IMS网络的P-CSCF就知道了请求的是语音电话。所以他发送一个AAR（AA-request）给PCRF，转发服务消息。这个AAR消息包含媒体信息，比如最大/最小带宽，IP数据流标识，codec等等。
 ** 3）【PCRF】策略决定 ** 
基于从P-CSCF接收到的服务信息，PCRF通过选择一个PCC规则和承载来决定EPS会话的策略。对于PCC规则，它选择voice-U规则来支持语音数据包。对于这个承载的QoS级别，选择QCI=1。由于语音数据包QoS不能被已经存在的默认承载的QoS支持，需要建立一个不同QoS等级的专用承载，接着修改EPS会话。
“Voice-U” rule: QCI = 1, ARP = 7, GBR (UL/DL) = 88 Kbps/88 Kbps, MBR (UL/DL) = 88 Kbps/88 Kbps,
Charging Rule: Offline, RTP Packet Filter
 ** 4）【PGW<-PCRF】PCC规则提供 ** 
PCRF给PGW传输PCC规则(“Voice-U”) 
 ** 5）【PGW】策略执行 ** 
一旦接收到这个PCC规则(“Voice-U”) ，PGW配置QoS和付费策略参数，并且把SDFQoS参数映射到专用承载QoS参数。
 - P-GW (SDF): QCI = 1, ARP = 7, GBR (UL/DL) = 88 Kbps/88 Kbps, MBR (UL/DL) = 88 Kbps/88 Kbps, SDF Template (UL/DL)=(UE IP, *, RTP, *, UDP) /(*, UE IP, RTP, *, UDP)
 - P-GW (Dedicated Bearer): QCI = 1, ARP = 7, GBR (UL/DL) = 88 Kbps/88 Kbps, MBR (UL/DL) = 88
Kbps/88 Kbps, TFT (UL/DL) = (UE IP, *, RTP, *, UDP)/(*, UE IP, RTP, *, UDP)
 ** 6）~7）【MME<-SGW<-PGW】专用承载创建请求 ** 
PGW给MME发送create bearer request消息来请求专用承载建立。这个消息 包含连接EPS承载标识LBI，专用承载ID，专用承载QoS和上行TFT信息。专用承载相关的信息包含LBI，是作为一个每一个专用承载所属的EPS会话的默认承载ID。从上图中，它是用语音会话的默认承载ID表示的。
 ** 8）【UE<-MME】请求专用承载上下文的激活 ** 
MME通过发送activate dedicated EPS bearer context request消息来请求专用承载上下文的激活。在这个消息中包含PGW发送的策略参数。
 ** 9）【UE】策略执行：专用承载上下文的激活 ** 
UE执行上行策略，并激活专用承载上下文。
UE: QCI = 1, GBR (UL) = 88 Kbps, MBR (UL) = 88K bps, TFT (UL) = (UE IP, *, RTP, *, UDP)
 ** 10）【UE->MME】通知专用承载上下文激活 ** 
UE通过发送activate dedicated EPS bearer Context accept消息通知MME，用于语音数据包的专用承载上下文已经激活了。
 ** 11）~12）【MME->SGW->PGW】专用承载创建的通知 ** 
MME给PGW通知专用承载已经创建了。
 ** 13）【PGW->PCRF】通知PCC策略已经适用 ** 
PGW给PCRF通知PCC规则已经执行了。
# IV. How Policy Control Affects the Way IP Packet Flows are Handled
我们将学习了PCC策略是如何影响EPS会话中IP数据流的处理的。图5描述了EPS会话中UE产生的IP数据流。在图5a中，Internet流量通过为Internet服务创建的默认承载从Internet PDN传输。对于语音服务，SIP信令流通过为语音服务创建的默认承载在IMS PDN（PCSCF）传输。语音媒体流通过为语音服务创建的专用承载和呼叫对端之间传输。
![这里写图片描述](http://img.blog.csdn.net/20151021080829745)
图6显示了应用于图5IP数据包流的策略控制。在途中，在图2中列出的策略参数在PGW中执行。PGW检测上下行的SDF，并对每一个SDF的每一个IP数据包执行策略控制。在EPS承载上，设置EPS承载QoS参数。
![这里写图片描述](http://img.blog.csdn.net/20151021080803848)
 ** 下行 ** 
IP数据包流到达PGW。根据PGW中设置的门状态，来自Internet和IMS网络的流量都是允许的。
 - SIP流量（APN=IMS）是i)由PGW的数据包过滤PF1作为SDF1检测的，ii）限制MBR（100Kbps）速率，iii）映射到默认承载，iv）限制APN-AMBR（100Kbps）。在eNB，流量时根据UE-AMBR的速率来调度的，并传送给UE。
 - 语音流量（APN=IMS）是i）由PGW的数据包铝PF2作为SDF2来检测的，ii）保证GBR速率，iii）映射到专用承载，iv）再次保证GBR。在eNB，流量时以GBR调度的，并传送给UE。
 - Internet流量（APN=Internet）是i）由PGW的数据包过滤PF3作为SDF3来检测的，ii）不限制速率，iii）映射到默认承载，iv）保证APN-AMBR速率，不受限。在eNB，流量是以UE-AMBR来调度的，并传送给UE。

 ** 上行 ** 
IP数据包从应用层到达UE。上行IP数据包通过上行数据包过滤器(UL TFT; PF1, PF2, PF3)映射到相应的的承载，并对数据包适用每一个承载的QoS。接着转发给PGW，PGW适用数据包过滤器(SDF template; PF1, PF2, PF3)检测SDF，在应用SDF QoS之后转发到目的地。

 - SIP流量是i）在UE的PF1映射到连接IMS APN的默认承载，ii）限制MBR（100Kbps）速率，iii）发送给eNB。在PGW中，它是i）再次限制MBR（100Kbps）速率，ii）有PF1作为SDF1检测，iii）保证SDF MBR（100Kbps），iv）接着转发给IMS网络。
 - 语音流量是i）在UE的PF1映射到连接IMS APN的专用承载，ii）以GBR发送给eNB，作为eNB调度。一旦到达PGW，它是i）有PF2作为SDF2检测，iii）保证SDF GBR（88Kbps）转发的。
 - Internet流量是i）在UE的PF3映射到连接Internet APN的默认承载上的，ii）保证APN-AMBR速率，不限制，iii）发送给eNB。一旦到达PGW，它是i）再次保证上行 APN-AMBR，ii）由PF3作为SDF3检测，iii）保证SDF MBR，不限制，iv）转发到Internet。

# V. EPS entity information

下面我们介绍在PCC规则在Internet和语音流量上执行之后每一个APN上EPS实体信息的变化情况。

##  5.1 Internet服务
图7展示了对Internet服务流量执行PCC规则之后EPS实体中的信息。在初始接入Internet时，PCRF选择一个PCC规则“Internet”，并转发给PGW。接着PGW使用这个规则对Internet服务的EPS默认承载执行QoS参数。这个默认承载保持激活知道UEdetach。

![这里写图片描述](http://img.blog.csdn.net/20151021083631584)

##  5.2 语音服务

图8展示了对语音服务榴莲根治性PCC规则之后保存在EPS实体中的信息。在实例中，一旦UE初始接入IMS网络，默认承载就建立了，就检测到了语音通话。在初始接入IMS网络时，PCRF选择一个PCC规则“voice-C”，PGW对SIP信令的EPS默认承载执行QoS参数。因为语音通话是在很短的时间内检测的，接着，PCRF选择voice-U规则，PGW对EPS专用承载执行QoS参数。

一旦语音通话结束，P-CSCF通知PCRF。接着PCRF发起EPS会话修改过程，把专用承载终止，删除UE/eNB/SGW/PGW中的专用承载上下文。但是，SIP信令的默认承载还是保持激活直到UE从LTE网络中detach。

![这里写图片描述](http://img.blog.csdn.net/20151021084213274)

# VI. closing

目前，我们已经学习了PCRF怎么决定PCC规则的以及当用户使用Internet和语音服务时PGW怎么执行规则的，并分析PCC规程。我们还学习了基于PCC规则PGW是怎么控制EPS承载的，并举例说明了策略控制是怎么影响IP数据包流的。
这篇文档覆盖了PCC规则，集中在QoS策略。下一个文档我们将介绍付费，讨论付费信息和过程。

# References
[1] 3GPP TS 29.212, “Policy and Charging Control over Gx Reference Point”
[2] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 2. Call Flow of Initial
Attach”, January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[3] Netmanias Technical Document, “LTE QoS: SDF and EPS Bearer QoS”, September 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5908
[4] Netmanias Technical Document, “Initial Attach procedure for VoLTE”, TBD
[5] Netmanias Technical Document, “LTE Charging: Offline”, TBD
[6] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010