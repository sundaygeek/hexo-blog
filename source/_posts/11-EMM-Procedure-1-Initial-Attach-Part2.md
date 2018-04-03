---
title: 11-EMM Procedure 1. Initial Attach Part2
date: 2018-04-03 22:19:18
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
EMM Procedure 1. Initial Attach
Part 2. Call Flow of Initial Attach

原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6102


----------
# I. Introduction

我们首先详细探讨EMM case-1：unknown UE的初始附着。这是当用户在签约LTE网络之后，首次打开UE并尝试附着到网络上。之前被分类为attach case-1。

这个文档组织如下：第二章讨论EMM case-1的详细过程，关注在在这个case中的功能需求。第三章描述在初始附着之后每一个实体中哪些信息发生了变化。

# II. Initial Attach Procedure

图1描述了EMM case1的初始附着过程，还有在每一个流程中的功能模块需求。

![这里写图片描述](http://img.blog.csdn.net/20150918081437944)

##  2.1 IMSI获取

图2展示了这个流程中的第一步。在这一步结束后，MME从UE获得IMSI。UE用attach request包含IMSI试图初始附着到网络上，MME从这个消息中获得IMSI。这一步可以细分为两小步：1）UE在无线连接同步之后待在初始状态2）UE建立ECM连接来传送attach request消息给MME。ECM建立连接过程又可以分为两个子过程，1）RRC连接建立过程2）S1信令连接建立过程。

![这里写图片描述](http://img.blog.csdn.net/20150918081934764)

**1）在无线连接同步之后初始状态**

为了UE请求附着到网络上，和eNB通信是必须的。所以UE通过PLMN选择和小区搜索过程选择一个eNB，并无线连接同步。接着用户可以和eNB通信了。在这个时候，UE是EMM-deregistered ECM-idle/rrc-idle状态。

**2）ECM连接建立**

在NAS层上，UE发送attach request消息（包含IMSI和UE网络能力信息）给MME的NAS层来请求初始附着。
为了能使attach request消息传输，在UE和MME之间的ECM连接时需要的。对于ECM连接，包含UE和eNB之间的RRC连接，eNB和MME之间的S1信令连接。NAS消息发送首先作为RRC消息通过RRC连接传输，然后通过S1信令连接作为S1AP消息传送（initial UE message初始UE消息）。

**（1）RRC连接建立**
RRC连接时建立在UE和eNB的RRC层之间的。一旦建立完成，在控制面传输消息到RRC层或者上层（NAS层）时使用RRC连接。建立RRC连接的过程如下所示：

1) [UE -> eNB] RRC Connection Request
UE通过发送RRC connection request消息给eNB来请求RRC连接。当UE请求attach，detach，TAU时都是用“Mobile Originating Signaling”，它是一个值，包含在Establishment Cause域。这个消息通过SRB0，CCCH，逻辑信道由UE发送给eNB。SRB0可以被这个小区的所有UE使用。

2) [UE <- eNB] RRC Connection Setup
eNB通过发送给UE RRC connection setup消息来为UE分配专有SRB1，这个消息是通过SRB0 CCCH传输的。UE的上行和下行无线资源是由eNB控制的。所以当完成这一步之后，UE通过使用RRC connection setup消息分配的SRB配置信息来使用无线资源。然后UE转到到EMM-deregistered ECM-idle RRC-connected状态。

3) [UE -> eNB] RRC Connection Setup Complete
UE通过使用SRB1 DCCH发送RRC connection setup complete消息通知eNB RRC连接建立完成了。为了有效的传输，当RRC Connection Setup Complete消息传输时，传输到NAS层的attach request消息也被发送到eNB（嵌入到RRC Connection Setup Complete消息的专用NAS信息域DedicatedInfoNAS）。

**（2）S1信令连接建立**

在eNB和MME之间的控制信息时通过S1-MME接口嵌入到S1AP消息中发送的。S1AP消息是通过为他们专有建立的S1信令连接来传送的。S1信令连接使用eNB和MME分配的ID对（eNB UE S1AP ID, MME UE S1AP ID）来定义标示UE。

在图2中，第一个NAS消息attach request消息在S1信令连接建立之前到达eNB。 eNB接着分配eNB UE S1AP ID用于S1信令连接的建立，并发送给MME attach request消息，嵌入到initial UE message中。attach request消息嵌入到initial UE message消息的NAS-PDU域。这个initial UE message消息包含以下信息字段：

Initial UE Message (eNB UE S1AP ID, NAS-PDU, TAI, ECGI, RRC Establishment Cause)

 - eNB UE S1AP ID: 在S1-MME接口上(Uplink)在eNB中标识UE
 - NAS-PDU: a NAS message (Attach Request)
 - TAI: 标识UE待在的TA
 - ECGI: 标识UE待在的小区
 - RRC Establishment Cause = mo-Signaling: 标识这个信令是UE产生的

当MME在S1-MME接口从eNB接收到这个initial UE message消息，MME为UE分配一个 MME S1AP UE ID。现在有了新分配的这个ID和之前分配的eNB UE S1AP ID，在这两个实体间的S1信令连接建立起来了。接下来MME S1AP UE ID会用于在S1-MME接口（下行）上MME标识UE。

**（3）ECM S1连接建立**

通过步骤（1）（2），在UE和MME的NAS层之前的ECM连接建立起来了。接着UE转移到EMM-registered ECM-connected RRC-connected状态。

**（4）IMSI获取**

MME的NAS层从UE的NAS发送的attach request消息中获取的UE的IMSI，通过UE的网络能力信息可以知道UE使用的安全算法和安全能力。

在从attach request消息中获取UE的IMSI和安全能力信息之后，MME执行鉴权和NAS安全建立过程用于NAS消息的安全传输（根据EPS AKA过程使用收集到的信息）。这两个过程--鉴权和NAS安全建立--是在2.2和2.3节描述。我们已经在LTE安全文档中详细介绍了，在这里我们将简单介绍一下。

##  2.2 鉴权

图3描述了UE和MME之间的鉴权过程。这个过程包含两部分，1）鉴权向量获取，MME从HSS获取鉴权向量，2）相互鉴权。在第一步中是在MME和HSS之间S6a接口使用Diameter协议来执行。第二步在UE和MME之间使用NAS协议执行。

![这里写图片描述](http://img.blog.csdn.net/20150918103141047)

**（1）鉴权向量的获取**
1）【MME->HSS】authentication information Request
MME向HSS发送authentication information Request消息，请求鉴权向量AV。这时候，在这个消息中包含UE的SN ID（服务网络ID）和IMSI，确保HSS返回的是UE当前服务网络信息。authentication information Request消息的主要参数包括：

Authentication Information Request (IMSI, SN ID)

 - IMSI: Subscriber identifier (a fixed value provisioned at HSS for a UE)
 - SN ID: indicates the serving network of a subscriber, and consists of an PLMN ID (MCC+MNC)

2）【HSS】产生鉴权向量
HSS使用在IMSI中的LTE K和SN ID来生成鉴权向量。鉴权向量生成包含下面两步：第一，HSS生成SQN和RAND，接着在加密函数中输入{LTE K,
SQN, RAND} 生成了{XRES, AUTN, CK, IK}. 接着在秘钥生成函数中输入 {SQN, SN ID, CK, IK} 生成KASME。

![这里写图片描述](http://img.blog.csdn.net/20150919082513160)

鉴权向量的最终格式是{RAND, AUTN, XRES, KASME}, 每一个鉴权向量单元的角色如下：

Authentication Vectors (RAND, AUTN, XRES, KASME)

 - RAND: HSS生成的随机数，并发送至UE。UE使用它来生成自己的鉴权向量
 - AUTN: HSS生成的鉴权令牌，并发送至UE，UE在生成自己的鉴权向量之后，和自己对比的鉴权令牌来鉴权网络
 - XRES: HSS生成的值。MME保留这个值而不发送给UE，接着MME和UE发过来的RES对比来鉴权用户。
 - KASME: 接入网络的顶层秘钥，由UE和HSS生成，并由HSS发送至MME。它作为MME和UE的基本秘钥用来生成NAS安全秘钥。

3）【MME<-HSS】传输鉴权向量
HSS在authentication information Response消息中包含鉴权向量发送给MME。MME接着使用这些信息和UE相互鉴权。

**（2）相互鉴权**
LTE需要相互鉴权。用户必须鉴权网络，网络必须鉴权用户。一旦MME从HSS接收到鉴权向量{RAND, AUTN, XRES, KASME}，它把RAND和AUTN发送给UE，UE使用它们来生成鉴权向量，并鉴权网络。MME保留着XRES, KASME用来用户鉴权和NAS安全秘钥生成。KASME是不会传输UE的。但是KASME的索引KSI-ASME是传输给UE的。相互鉴权过程如下：

4）【UE<-MME】MME请求用户鉴权
MME传输authentication Request消息（RAND, AUTN, KSIASME）给UE。

5）【UE】用户鉴权网络：生成鉴权向量并鉴权网络
一旦接收到authentication Request消息（RAND, AUTN, KSI-ASME），UE首先从AUTN中生成SQN，接着像图4HSS那样生成鉴权向量。接着UE对比自己生成的AUTN-UE和接收到的AUTN来鉴权网络。并存储 KSI-ASME作为K-ASME的索引。

6）【UE->MME】用户的RES传输给MME
通过对比AUTN，用户完成网络鉴权之后，UE发送自己生成的RES到MME，包含在authentication Response消息中。这样MME就可以鉴权用户了。

7）【MME】网络鉴权UE
一旦接收到authentication Response消息，MME对比UE发过来的RES和从HSS接收到的XRES来鉴权用户。

一旦上述步骤完成后，UE和网络就完成了相互鉴权。现在将开始建立NAS安全。

##  2.3 NAS安全建立

一旦用户鉴权完成，MME发起NAS安全建立过程为了NAS消息可以安全的传输。图5展示NAS安全建立过程的流程。

![这里写图片描述](http://img.blog.csdn.net/20150919084545483)

1）【MME】生成NAS安全秘钥
MME从attach Request消息中选择应用于NAS消息的加密和完整性算法。接着，从K-ASME中生成NAS完整性秘钥和NAS加密秘钥K-NASint/K-NASenc，应用于NAS消息。

2）【UE<-MME】帮助UE生成NAS安全秘钥
MME通过Security mode command消息(KSIASME, Security Algorithm, NAS-MAC)通知给UE选择的加密算法，帮助UE生成NAS安全秘钥。这个消息时包含NAS-MAC完整性保护的。

3）【UE】产生NAS安全秘钥

当UE收到Security mode command消息，UE通过使用MME选择的NAS安全算法来生成NAS安全秘钥(KNASint and KNASenc)。并通过NAS完整性秘钥(KNASint)来对Security mode command消息进行完整性检查。如果完整性检查通过，表示NAS安全秘钥成功设置并工作正常。

4）【UE->MME】NAS安全秘钥生成完毕
UE通过发送Security mode complete消息通知给MME NAS安全秘钥成功生成，接着可以使用生成的秘钥来加密和完整性保护。

在完整上述步骤之后，NAS安全建立过程结束。接着来UE和MME之间的消息都是安全传输的（使用加密和完整性保护）。

##  2.4 位置更新

一旦完成鉴权和NAS安全建立过程，MME需要注册签约用户，并找出来签约用户使用的服务。MME通知HSS签约用户已经注册了并驻留在它的TA，接着从HSS下载签约用户信息。所有这些过程都是通过MME和HSS之间S6a接口的Diameter协议来执行位置更新过程来完成的。具体过程如图6所示。

![这里写图片描述](http://img.blog.csdn.net/20150919090030241)

1）【MME->HSS】通告UE位置
MME向HSS发送update location Request（IMSI，MME ID）消息为了通告UE的注册和获取UE的签约信息。

2）【HSS】UE位置更新
HSS注册MME ID来指示UE驻留的哪个MME。

3）【MME<-HSS】用户签约信息的传输
HSS把用户的签约信息包含在update location answer消息中发送给MME，MME创建这个签约用户的EPS会话和默认EPS承载。包含在update location answer消息中的签约信息如下：

Update Location Answer (IMSI, Subscribed APN, Subscribed P-GW ID, Subscribed QoS Profile)

 - Subscribed APN: 用户签约的APN (e.g. Internet service)
 - Subscribed P-GW ID: 用户接入签约APN通过的那个PGW的ID
 - Subscribed QoS Profile(UE-AMBR(UL/DL), QCI, ARP, APN-AMBR(UL/DL))
    - UE-AMBR (UL/DL): MME决定和eNB控制的UE具有所有的nonGBR承载的聚合带宽
    - QCI, ARP, APN-AMBR (UL/DL): 应用于签约APN的QoS

4）【MME】存储签约信息
MME从HSS接收到update location answer消息后，存储这个消息中的签约信息。

从下载的签约信息，MME可以检查用户签约的服务，使用什么QoS级别的资源来连接到哪个APN。

##  2.5 EPS会话建立

MME基于签约信息建立EPS会话和默认EPS承载。MME为每一个用户提供满足用户签约QoS的网络/无线资源。图7/8描述了EPS会话和默认EPS承载建立过程。

![这里写图片描述](http://img.blog.csdn.net/20150919092612216)

1）【MME】分配EPS承载ID
MME从5-15选择一个值，分配作为EPS承载ID（EBI）来为新附着的用户连接默认EPS承载。

2）【MME】选择PGW
MME检查从HSS接收到的APN，并决定使用哪个PGW来接入这个APN。这个决定是基于从HSS接收到的签约信息（PGW ID）。或者如果没有这些信息，MME请求DNS服务器获得APN FQDN(e.g. internet.apn.epc.mnc05.mcc450.3gppnetwork.org), 并从按照PGW选择策略返回的PGW IP地址列表中选择一个。这时候，也需要选择一个SGW来到达PGW。

3）~4）请求EPS会话创建
MME通过给PGW发送create session Request消息来请求EPS会话和默认EPS承载的创建。在这个消息中，MME包含从HSS接收到的用户签约信息，当请求PCRF创建EPS会话时，PGW可以使用这些信息。这时候，UE-AMBR还没有包含，因为它是由MME决定的。

3）【MME->SGW】请求EPS会话创建
MME和SGW在控制面使用GTP协议在S11接口上通信。MME发送给第二步选择的SGW create session Request消息，包含以下参数：

Create Session Request (IMSI, EPS Bearer ID, P-GW IP, APN, Subscribed Profile (QCI, ARP, APNAMBR (UL/DL)), ECGI, TAI)

 - IMSI: 固定的用户ID
 - EPS Bearer ID: 由MME分配的默认EPS承载ID
 - P-GW IP: MME选择用于EPS会话/承载创建的PGW IP地址
 - APN: 用户签约的APN
 - Subscribed Profile (QCI, ARP, APN-AMBR (UL/DL)): 建立默认EPS承载时使用的QoS信息
 - ECGI: 用户驻留的小区
 - TAI: 用户驻留的TA

4）【SGW->PGW】请求EPS会话的创建
SGW和PGW在S5接口上用户面和控制面使用GTP（UP：GTP-U，CP：GTP-C）协议通信。SGW在create session Request消息中分配S5 TEID来建立到PGW的S5 GTP。接着在create session request消息中发送这个ID和其他参数给PGW。

Create Session Request (IMSI, EPS Bearer ID, S5 S-GW TEID, APN, Subscribed Profile (QCI, ARP, APN- AMBR (UL/DL)), ECGI, TAI)

5）【S5 承载：下行】
一旦4）完成之后，下行S5 GTP-U隧道就建立起来了，允许PGW发送下行流量给SGW。在图7和8中，分配和发送GTP隧道TEID的实体被标记为实点，接收的一个被标记为虚点。

6）【PGW】分配用户IP地址
PGW一旦接收到create session request消息，意识到用户试图通过IMSI接入网络。所以PGW给UE分配IP地址，让UE可以使用IP。

7）【PGW->PCRF】通知EPS会话建立
PGW和PCRF通过Gx接口使用Diameter协议通信。当创建了用户的EPS会话，用户的资源配置和QoS控制都必须基于用户订阅的服务来决定。PCRF负责所有接入网络UE的接入策略。所以，PGW提供给PCRF用户的签约信息，并获取资源分配和网络运营商策略的PCRF授权。从UE的签约信息（从MME接收到的），PGW获取用于PCRF运营商策略决策制定的信息，并通过CCR消息（CC-request）发送这些信息给PCRF。这个消息实例如下：
CCR (IMSI, UE IP, PDN ID (APN), Subscribed QoS Profile (QCI, ARP, APN-AMBR (UL/DL)), ECGI,TAI)：

 - IMSI: 固定的用户ID
 - UE IP: 正在服务中的用户使用的IP地址
 - PDN ID: 用户使用的APN
 - Subscribed Profile (QCI, ARP, APN-AMBR (UL/DL)): 建立默认EPS承载时应用的QoS信息
 - ECGI: UE驻留的小区
 - TAI: UE驻留的TA

8）【PCRF->SPR】请求接入配置
PCRF向SPR请求用户接入配置文件来决定用户的PCC策略

9）【PCRF<-SPR】返回接入配置
SPR返回用户的接入配置文件。这个配置文件包含的信息例如，SDF Filter, QCI, ARP, APN-AMBR (UL/DL), Charging Method (e.g. Offline), Changing Reporting Action (e.g. Start Reporting ECGI, TAI),等等

10）【PCRF】决定策略
PCRF决定EPS会话建立（基于用户接入配置文件）的PCC策略。

11）【PGW<-PCRF】确认EPS会话建立
PCRF传输决定好的PCC策略给PGW，包含在CCA（CC-answer）消息中。这个消息的实例如下：
CCA (IMSI, PCC Rule (SDF Filter, QCI, ARP, APN-AMBR (UL/DL), Charging=Offline, Change Reporting Action (Start Reporting ECGI, TAI))

12）策略实施
PGW应用从PCRF接收到的PCC策略。PCC策略应用到每一个SDF上，所以PGW需要建立SDF和EPS承载之间的映射，并准备应用QoS配置文件到默认EPS承载上。

13）~15）EPS session create response
PGW通过发送create session response消息通知MME，应用于建立EPS会话和默认EPS承载的QoS信息。PCRF可能会保存这个从HSS接收到的值或者选择一个新的值。

13）【SGW<-PGW】EPS session response
PGW为建立到SGW的S5 GTP分配S5 TEID。接着在create session response消息中包含S5 PGW TEID和QoS配置文件应用于默认EPS承载，并发送给SGW作为对create session request消息的响应。
Create Session Response (UE IP, EPS Bearer ID, S5 P-GW TEID, Authorized QoS Profile (QCI, ARP,APN-AMBR (UL/DL)), TFT (UL), Change Reporting Action (Start Reporting ECGI, TAI))

14）【S5承载：上行】S5承载建立
在完成13）之后建立了上行S5 GTP-U隧道，允许SGW和PGW剑豪上下行流量。

15）【MME<-SGW】EPS session create response
当从PGW接收到create session response消息后，SGW保留上行S5 TEID用于上行流量，并允许S1GTP 隧道的S1 TEID用于S1承载。在处理完这个消息后，SGW在处理完的消息上加入新分配的S1 SGW TEID，然后发送到MME，作为对3）的create session request的响应。

16）【MME】为什么MME保留S5 PGW TEID
一旦附着到网络，如果UE执行TAU或者切换，SGW可能发生改变。所以MME通知UE新SGW的上行S5 TEID，这样新SGW可以传输上行流量给PGW。

17）【S1承载：上行】
完成15）之后建立了上行S1 GTP隧道。但是因为eNB还没有S1 SGW TEID，所以这是还不能给SGW传输上行流量。

18）【MME】计算ue-ambr
这时候，MME返回给attach accept消息给UE作为对attach request消息的响应，并通过控制eNB来转呗e-rab建立（eg，为无线链路和S1承载分配资源）。MME计算ue-ambr值发送给eNB。MME已经接收到ue-ambr值，包含在签约信息中。但是MME可以调整这个值，使之不超过每一个APN的全部apn-ambr，并分配它。

![这里写图片描述](http://img.blog.csdn.net/20150921120911922)

19）决定E-rab和NAS信令需要的信息
在从PGW接收到create session response消息之后，MME知道已经指定和分配给用户的资源。接着，MME负责E-RAB的建立，并控制eNB和SGW。最后，MME决定E-RAB建立所需要的资源和NAS信令需要的信息：

 - 分配给UE GUTI代替IMSI
 - 决定和控制TAU相关的参数（TAI列表分配，tau定时器值）
 - 决定eNB使用的ue-ambr值
 - 分配e-rab ID

20）【UE<-MME】attach accept
MME在attach accept消息中包含下面信息：PGW分配的UE IP地址，GUTI，TAI list，EPS  bearer ID，MME自己分配的UE-AMBR，从SGW接收到的QoS参数，并发送给UE作为对attach request的响应。
这个消息是包含在initial context setup request消息中通过S1信令连接传送的，接着通过RRC连接包含在RRC connection reconfiguration消息中。

21）【MME】生成K-eNB
MME从K-asme中生成K-enb，（AS安全基础秘钥）。这是为了确保eNB可以生成AS安全秘钥用于eNB和UE之间无线链路的安全传输。

22）【eNB<-MME】请求e-rab建立
MME发送initial context setup request消息，接着eNB和SGW建立起S1承载，和UE建立起DRB承载。这个消息包含以下信息：
Initial Context Setup Request (UE-AMBR (UL/DL), E-RAB ID, E-RAB QoS (QCI, ARP), S1 S-GW TEID,KeNB, , UE Security Algorithm, NAS-PDU)：

 - UE-AMBR(UL/DL):只能被eNB控制的QoS参数
 - E-RAB ID: 由MME分配，并由eNB作为EPS bearer ID使用
 - E-RAB QoS: 由MME以及与从PGW接收到的EPS bearer QoS来决定
 - S1 S-GW TEID: 从SGW接收到的上行S1 TEID
 - KeNB: 由MME从Kasme生成的，用于eNB中AS安全秘钥的生成
 - UE Security Algorithm: 包含在Attach Request消息中，和KeNB一起用来eNB建立AS安全
 - NAS-PDU: NAS消息 (Attach Accept)

23) [S1 承载: 上行]
一旦22）完成，获得了S1 SGW TEID，eNB可以传输上行流量到SGW。当eNB接收到MME的Initial Context Setup Request消息来请求E-RAB建立，eNB通过发送attach accept消息给UE来建立DRB。接着eNB在Initial Context Setup Response消息中包含下行S1 TEID来建立S1承载，并发送Initial Context Setup Response消息到MME来作为Initial Context Setup request消息的响应，所以MME可以转发这个消息给SGW。

24) ~ 27) AS安全建立
一旦接收到MME的initial context setup request消息，eNB试图和UE通信建立DRB。为了在无线链路上安全的通信，eNB在发送信息给UE之间需要执行AS安全建立过程。

24) [eNB] 产生AS安全秘钥
eNB从KeNB中生成AS安全秘钥用于RRC消息和用户流量的安全传输。eNB从MME传输过来的安全算法中为RRC消息选择加密和完整性算法，并未用户流量选择加密算法。接着，从K-eNB中生成KRRCint/KRRCenc（RRC完整性和加密秘钥）和KUPenc（用于用户数据的加密）。

25) [UE <- eNB] 帮助UE生成AS安全秘钥
eNB通过通知给UE选择的AS安全算法来帮助UE生成AS安全秘钥 (KRRCint, KRRCenc and KUPenc)，（使用Security Mode Command(AS Security Algorithm, MAC-I)消息来传输）。eNB发送完整性保护的RRC消息，（包含mac-I）.

26) [UE] 生成AS加密秘钥
一旦接受到Security Mode Command 消息，UE通过使用eNB选择的AS安全算法来生成AS安全秘钥，并执行Security Mode Command 消息的完整性检查。

27) [UE -> eNB] AS秘钥生成完成
一旦完成对Security Mode Command 消息的完整性检查，AS安全秘钥被成功的建立起来，并准备在UE和eNB之间使用。UE通过使用Security Mode complete (MAC-I) 消息指示eNB AS安全秘钥已经产生。UE使用RRC完整性秘钥来对发送的消息进行完整性保护。随着在无线链路接上AS安全建立过程结束，在随后的无线连接上的RRC消息交换都是加密和完整性保护的，并用户消息是加密传送的。现在eNB开始建立DRB

28) ~ 29) DRB建立
28) [UE <- eNB] 重配RRC连接
eNB分配上下行 DRB ID，并从E-RAB QoS中配置DRB QoS参数来建立无线连接的DRB。接着通过安全的RRC连接发送RRC connection reconfiguration消息给UE。RRC连接上在UE发送attach request消息时建立。现在RRC连接必须重配，因为UE被允许接入网络后需要根据网络分配的资源来配置参数。UE的RRC层根据RRC connection reconfiguration消息中的配置参数来分配无线资源。接着从RRC connection reconfiguration中提取attach accept消息，并发送到NAS层。当UE的NAS层接收到这个消息，从这个消息中蝴蝶UE IP地址和GUTI，用于接下来的通信需要。

29) [DRB 建立: 上行和下行] DRB建立完成
一旦28）完成后，UE可以和eNB之间传输上下行数据。

30) [eNB -> S-GW] E-RAB Setup Response
eNB为S1承载分配下行S1 TEID (S1 eNB TEID) 。接着在 Initial Context Setup Response消息中包含分配的ID发送给MME，作为 Initial Context Setup request消息的响应，接着MME把这个消息转发给SGW。

31) [eNB] 为S1承载分配下行TEID
一旦29）完成后，eNB为S1承载分配了下行TEID，建立下行S1 GTP-U隧道。因为SGW并不知道是否建立，现在它还不能传输下行数据到eNB。

32) [UE -> MME] 发送Attach Complete 消息
UE发送Attach Complete消息给MME作为20）的消息的响应。Attach Complete消息是在RRC连接上通过UL Information Transfer消息，在S1信令连接上通过Uplink NAS Transport 消息传输的。

33) [UE][MME] EMM State
现在UE和MME处于EMM-Registered状态。如果在20）从MME接收到的是Attach Reject消息，UE必须释放ECM/RRC连接，并且转移到EMM-deregistered状态。

34) [MME -> S-GW] 请求S1承载修改
MME通过Modify Bearer Request消息转发从eNB接收到的下行S1 TEID(S1 eNB TEID)给SGW。

35) [MME <- S-GW] 响应S1 Bearer Modification Request
SGW发送给MME Modify Bearer Response消息作为对Modify Bearer Request消息的响应。现在SGW准备传输下行S1数据。

36) [S1 Bearer: Downlink] S1 Bearer Setup Complete
在35）完成S1承载建立过程。随着S1承载建立的完成，eNB和SGW可以相互交换数据了。现在从UE一直到PGW的默认承载终于建立起来了，允许在UE和PGW之间传输上下行EPS承载通信。

# III. EPS Entity Information: Before/After Initial Attach
在这一章，我们将讨论在“EMM Case 1: Initial Attach by Unknown UE”过程前后EPS实体的EMM状态的变化。存储在EPS实体中的信息将会被分组给UE ID信息，UE 位置信息，安全上下文信息，安全上下文信息，EPS会话/承载信息，如图9所示。

![这里写图片描述](http://img.blog.csdn.net/20150921153019850)

##  3.1 Initial Attach之前
图10描述了在“EMM Case 1: Initial Attach by Unknown UE”过程之前每一个实体中的信息。因为EMM case-1是未知用户的初始附着，所以仅包含网络提供的信息。

![这里写图片描述](http://img.blog.csdn.net/20150921153050505)

 - UE ID information: 在UE, HSS and SPR.中提供的用户的IMSI。
 - UE Location information: 在UE和网络都没有任何关于UE位置的信息。
 - Security Context information: 在UE and HSS提供了用于用户鉴权的LTE 主秘钥
 - EPS Session/Bearer information: 用户签约信息 (Default APN, Subscribed QCI, ARP,UE-AMBR, APN-AMBR, etc.) 和用户接入配置 (Subscribed QCI, ARP, APN-AMBR, etc.)在HSS and SPR中提供。

##  3.2 Initial Attach之后
图11描述了在“EMM Case 1: Initial Attach by Unknown UE”过程之后EPS实体中存储的信息。因为用户已经注册到网络上，所有必要的信息都分配了，信息（用于UE安全接收用户数据和用于在任何设定的位置以想要的服务质量使用服务的信息）都分配了。我们将学习在initial attach之后什么信息发生了变化。

![这里写图片描述](http://img.blog.csdn.net/20150921153154280)

UE ID Information的变化

 - IMSI: 在EPS承载/会话建立之后，由UE通过attach request消息传输的IMSI被加入到MME, SGW, P-GW and PCRF。
 - GUTI: 在NAS消息中使用由MME分配的用来代替IMSI的GUTI加入到MME和UE.
 - UE IP address: PGW分配的UE IP地址被加入到P-GW, PCRF, MME and UE.
 - C-RNTI: eNB分配的C-RNTI 用来空中接口物理层识别UE被加入到到eNB和UE.
 - UE S1AP ID: eNB UE S1AP ID和MME UE S1AP ID接入到eNB和MME用来S1-MME接口S1AP消息中标识用户。

UE Location Information的变化

 - ECGI: 用户驻留小区信息加入到UE, eNB, MME, S-GW, P-GW and PCRF. 每次用户移动到一个新小区，MME通知PGW，接着通知PCRF，用PCRF设定的Change Reporting Action策略通知小区。
 - TAI: 用户驻留的TA加入到UE, eNB, MME, S-GW, P-GW and PCRF. 每次用户移动到一个新的TA，MME通知PGW，接着通知PCRF，用PCRF设定的Change Reporting Action策略通知TA。
 - TAI list: UE不需要tau就可以进入的TA列表加入到MME和UE
 - MME ID: 用户附着打MME信息加入到HSS中

Security Context Information的改变

 - NAS Security Info: NAS安全上下文信息加入到UE和MME中
 - AS Security Info: AS安全上下文加入到UE和eNB中

EPS Session/Bearer Information的改变

 - APN in Use: 在EPS会话创建时加入到MME, S-GW, P-GW, PCRF and UE 
 - EPS Bearer ID: 加入到MME和默认承载创建的实体，像UE, eNB, S-GW and P-GW.
 - DRB ID: 加入到UE 和eNB使之能在无线连接上通信
 - E-RAB ID: 在E-RAB创建时加入到eNB和MME中
 - S1 TEID (UL/DL): 在S1承载建立时加入到eNB, S-GW and MME
 - S5 TEID (UL/DL): 在S5承载建立时加入到S-GW, P-GW and MME
 - QCI: 分配用于所有类型的SDF和EPS承载，加入到UE, eNB, MME, S-GW, P-GW and PCRF. 这个值是由PCRF提供的。
 - ARP: 分配用于所有类型的SDF和EPS承载，加入到eNB, MME, S-GW, P-GW and PCRF,但是不分配给UE(unlike QCI). 这个值是由PCRF提供的。
 - UE-AMBR (UL/DL): 在EPS会话和承载创建时加入到MME和eNB中。是由MME计算得到的。
 - APN-AMBR (UL/DL): 在EPS会话和承载创建时加入到MME和eNB中。这个值是由PCRF提供的。UE只有APN-AMBR（UL）。
 - TFT (UL/DL): 在EPS承载创建时加入到PGW和UE中。PGW上下行都有这个值，但是UE只有上行有。
 - SDF Filter: 在EPS会话创建时加入到PCRF。
 - Subscribed Profile: 在用户位置更新过程时在从HSS中下载签约信息时加入到MME中。

# IV. Closing

我们讨论了在签约服务之后第一次附着网络initial attach过程。另外，在上面的文档讨论了四种case。在接下来我们将讨论detach过程。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “EMM Procedure 1. Initial Attach – Part 1. Cases of Initial Attach”,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6098
[3] Netmanias Technical Document, “LTE Security II: NAS and AS Security”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5903
[4] Netmanias Technical Document, “LTE Security I: LTE Security Concept and LTE Authentication”, July
http://www.netmanias.com/en/?m=view&id=techdocs&no=5902
[5] Netmanias Technical Document, “LTE QoS: SDF and EPS Bearer QoS”, September 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5908
[6] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.
