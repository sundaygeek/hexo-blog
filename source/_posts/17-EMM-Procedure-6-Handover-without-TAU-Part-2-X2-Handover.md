---
title: 17-EMM Procedure 6. Handover without TAU - Part 2. X2 Handover
date: 2016-04-03 22:34:57
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6257

# I. Introduction

在前一篇文档中我们讨论了LTE切换相关的过程，并学习了LTE切换是一个 UEassisted / network - controlled 的过程，即UE报告，eNB决定。这篇文档将讨论X2切换。所以我们假设源和目的eNB都连接到同一个MME/SGW，并且位于同一个TA，也就是在UE的TAI列表中。第二章将讨论X2切换的概念，第三章详细介绍X2切换的过程，最后第四章将总结在X2切换前后EPS实体的信息是怎么变化的。

# II. Concept of X2 Handover

##  2.1 X2 Protocol Stacks

X2切换是通过X2接口在源eNB和目的eNB之间执行的。在一个LTE网络中，两个eNB可以通过X2接口直接通信，这不同于在2/3G网络的情况。在2/3G网络中，eNB获得邻eNB状态的唯一的方法就是通过数据核心节点的控制。但是在LTE网络，允许eNB直接通过X2接口来交换状态信息，并在没有EPC节点的介入下来独立的执行切换。图1展示了控制面和用户面X2接口上的协议栈。

![这里写图片描述](http://img.blog.csdn.net/20151009084518731)

在控制面，两个eNB通过SCTP数据流控制传输协议提供给多个用户X2AP X2应用层协议信令。在X2AP层，使用eNB UE X2AP ID (Old eNB UE X2AP ID, New eNB UE X2AP ID)来标识用户。
在数据面，两个eNB通过GPRS隧道协议GTP协议连接。为每一个用户产生一个独一无二的GTP隧道，并且每一个隧道被分配的TEID标识。

##  2.2 X2AP功能

表1列出了通过X2AP信令执行的功能，并且和每一个功能相关的基本过程。如下所示，X2AP信令信息可以粗略地分为两种，一种是和负载/接口相关（例如，负载管理），另一种是和切换相关（例如，移动性管理，移动参数管理，移动鲁棒性优化）。

![这里写图片描述](http://img.blog.csdn.net/20151009084617310)

对比2/3G网络，像LTE宽带网络具有少的小区覆盖，但是有很多基站来覆盖。所以，像2/3G那样传统的配置和管理网络方式将不再有效。为了处理这个问题，在LTE网络中，定义了X2AP协议来提供自组网SON功能。这就允许eNB可以连接到邻eNB，获取他们的状态信息，并使用获取的信息自动的配置和优化参数。在表1列出的X2AP功能，这些和SON相关的如下：

**Load Management**: 通过在两个eNB之间交换负载和接口信息来增强性能干涉。
**eNB Configuration Update**: 执行自动eNB配置
**Mobility Parameters Management**: 在eNB之间协商切换触发设置信息，并使用这些信息来切换优化
**Mobility Robustness Optimization**: 对切换失败事件提供信息
**Energy Saving**: 通过交换交换小区的激活或者去激活来帮助eNB减少能量消耗

##  2.3 和移动向管理相关的X2消息

表2显示了用于移动性管理功能的消息，和切换相关的将会在第三章讨论。我可以看出在切换准备阶段需要来自目标eNB的响应消息。

**Handover Request message**: 这个小区在切换准备阶段使用。它由源eNB发送给目的eNB，包含用户的UE上下文。
**Handover Request Acknowledge message**: 这个消息在切换准备阶段使用，在目的eNB成功完成资源分配之后由目的eNB发送给源eNB。
**Handover Preparation Failure message**: 这个消息在切换准备阶段使用，在目的eNB分配资源失败时由目的eNB发送给源eNB。
**SN Status Transfer message**: 这个消息在切换执行阶段使用，它由源eNB发送给目的eNB来指示从哪一个数据包开始应该接收或者发送。
**UE Context Release message**: 这个消息在切换完成阶段使用，由目的eNB发送给源eNB，来请求UE上下文的释放。
**Handover Cancel message**: 这个消息在切换准备阶段使用，当需要删除一个正在准备的切换时由源eNB发送给目的eNB。

![这里写图片描述](http://img.blog.csdn.net/20151009084702259)

##  2.4 简要X2切换过程

如前一个文档所述，X2切换过程包括准备，执行，完成阶段。在我们深入讨论之前，我们将简要预览一下X2切花过程。如2描述了在X2切换前，中，后需要的过程。为了方面SGW和PGW被标记为SAE-GW，源和目的eNB被标记为SeNB和TeNB。

![这里写图片描述](http://img.blog.csdn.net/20151009084735359)

**Before X2 Handover**
在上图中，UE是由eNB A服务，当UE检测到一个测量事件，UE给eNB A发送测量报告消息。

**X2 Handover Preparation**
源eNB基于自己保存的邻区列表信息和包含在测量报告消息中的邻区信号强度信息来选择目的eNB切换。接着，通过X2信令和目的eNB准备X2切换。这时，目的eNB提前分配资源，使得在源eNB用户可用的服务现在在目的eNB也可用了。为了切换更快的切换，目的eNB发送给源eNB 目的eNB需要的所有UE信息，同样也把这些信息发送给UE，来发起切换执行阶段。目的eNB分配的资源如下：

 - 当源eNB给目的eNB发送handover request消息包含用户UE上下文（1）。
 - 目的eNB：
     - 获取S1承载信息(S1 S-GW TEID)来建立上行S1承载传输上行数据包（2）.
     - 为X2传输协议（GTP-U）分配TEID，用于在UE试图接入目的eNB时接收下行数据。
     - 分配DRB资源和C-RNTI供UE在目的小区中使用。
     - 发送handover request ack消息给源eNB（3）。
 - 一旦接受到这个消息，源eNB建立X2传输承载用于发送下行数据包（4）。

**X2 Handover Execution**
一旦在两个eNB之间切换准备完成，可以开始执行切换了。

 - 源eNB
     - 通过发送handover command消息（包含接入目的小区需要的所有信息）指示UE来执行切换到目的小区。（1）
     - 通过发送给目的eNB一个SN status transfer消息通知目的eNB当和UE通信时哪一个UL/DL数据包需要接收或者发送（2）
     - 通过X2传输承载转发从SGW接收到的下行数据包给目的eNB（3）
 - UE从源eNB去附着，并接入到目的eNB（4）.
 - 一旦UE成功接入，目的eNB有能力发送和接收数据包了（5）。

**X2 Handover Completion**
到目前为止，从源eNB决定执行切换开始，在切换执行阶段到UE最终连接到目的eNB，之间执行的所有过程都没有切换相关的信息报告给EPC（MME）。现在切换完成了，目的eNB通知EPC：

 - 一旦UE接入完成，目的eNB通知EPC，并发送path switch request消息给MME，通知EPC承载路径可以修改了。（1）
 - 当接收到这个消息，MME觉察到UE新的服务小区，接着需求请求SGW修改S1承载（2）。
 - 一旦请求，SGW建立下行S1承载(S1 Target eNB TEID)连接到目的eNB。接着SGW停止发送下行数据包到源eNB，并通过新建立的下行承载开始向目的eNB发送下行数据包。（3）
 - MME通知目的eNB，下行S1承载路径已经修改完成（4）。
 - 目的eNB发送给源eNB一个UE Context Release消息，允许源eNB释放UE上下文。（5）

**After X2 Handover**
UE开始通过eNB B获得服务。

##  2.5 X2切换前后UE状态和连接信息
图3描述了在用户面控制面建立的连接，和UE、MME状态
**Before X2 Handover**
UE处于EMM-Registered and ECM/RRC-Connected状态并保持E-UTRAN和EPC分配的资源。
**During X2 Handover**
即使在切换状态，NAS层UE状态保持不变，X2承载和X2信令连接通过X2接口建立。在图3中，第2步展示了在切换执行阶段当切换中断时的连接和状态。这时候，没有无线连接处于激活状态，但是UE一直保持connected。
**After X2 Handover**
UE保持在EMM-Registered and ECM/RRC-Connected状态。用户面的E-RAB(DRB+S1承载)路径转移到新的eNB上，控制面的新RRc连接和S1信令连接(eNB(B) S1AP UE ID)也建立起来了。

![这里写图片描述](http://img.blog.csdn.net/20151009084840231)

# III. Procedure of X2 Handover

现在我们来详细讨论X2切换过程。图4描述了在X2切换之前的EPS承载和信令连接，和详细的X2切换准备过程。

![这里写图片描述](http://img.blog.csdn.net/20151009084918089)

**Before Handover**
1) [UE -> eNB] Measurement Report
随着测量事件触发，UE测量邻区的信号强度，并发送measurement report消息给响应的eNB。

**Handover Preparation**
2) [Source eNB] Handover Decision
源eNB基于measurement report消息包含的信息和自己保存的邻区列表信息选择目标eNB。在实际切换中，有不止一个目标eNB选择或者不是UE报告的邻区被选择为目标小区。但是，我假设只有一个eNB包含在measurement report消息里面，这个eNB被选择为目标eNB。

3) [Source eNB] Deriving the AS Security Base Key (KeNB*) to be Used by the Target eNB
当切换发生时，UE的服务eNB发生转换。在转换期间，RRC信令消息和用户数据包不得不无缝的和安全的传送。在无线连接上，是AS安全秘钥确保这些数据的安全传输。AS安全秘钥是从KeNB（AS安全基础秘钥）衍生的。KeNB是在用户鉴权后从KASME派生的，由MME发送给eNB。但是，因为X2切换是在没有EPC干预下在两个eNB之间执行的，目的eNB不能从MME中获得KeNB*（KeNB被目的eNB使用）。所以，源eNB生成这个秘钥然后发送给目的eNB。这样，一旦源eNB决定执行切换，它会首先产生KeNB *，如图5所示。KeNB *从KeNB/目标小区PCI和频率（EARFCN-DL）中派生。
![这里写图片描述](http://img.blog.csdn.net/20151009084958710)
4) [Source eNB -> Target eNB] 请求X2切换
源eNB给目的eNB发送handover request消息请求切换。通过这个消息，传输存储的UE上下文信息和UE历史信息（UE连接目的小区优先于最后切换到的目标小区）。这个信息包括如下：
**Handover Request** (Target Cell ID, UE Context Info(UE-AMBR, UE Security Capability, KeNB*, E-RAB to be setup (E-RAB ID, QCI, ARP, S1 S-GW TEID), RRC Context), UE History Info)
 - Target Cell ID: the target cell’s ECGI
 - UE Context Info: UE context stored at the source eNB
     - UE-AMBR: provided by HSS, but can be modified by MME. This value can be set for eNB, and used to control the aggregated MBR value of non-GBR bearers.
     - UE Security Capability: security algorithms supported by UE (encryption and integrity algorithm)
     - KeNB*: AS security base key generated by the source eNB for the target eNB’s use, i.e. KeNB to be used by the target eNB
     - E-RAB to be setup: UE’s E-RAB information stored at the source eNB
 - UE History Info: information about the cells that UE has accessed while staying Active, including each cell’s ECGI, type and the duration of UE’s stay in the cell.

5) [Target eNB] 准备X2切换
一旦接收到handover request消息，target eNB开始切换准备为UE提供无缝的服务。
(i) 首先，target eNB从source eNB接收的KeNB*生成AS安全秘钥(KRRCint, KRRCenc, KUPenc)。使用这些秘钥，target eNB可以和UE安全的通信。
(ii) 接着，target eNB基于要建立的E-RAB信息检查source eNB提供的相同的QoS是否在target eNB中可用。如果可用，target eNB通过存储在source eNB的上行S1承载信息(S1 S-GW TEID)建立上行S1承载连接SGW。
(iii) 接着，基于E-RAB QoS信息，target eNB在无线链路上为UE保留RRc资源，并分配C-RNTI。
(iv) 当UE执行切换时，到达source eNB的下行数据包需要转发给target eNB。为了实现这个，target eNB分配X2 Target eNB TEID (DL TEID of X2 GTP tunnel) ，source eNB可以建立X2传输承载（GTP隧道）。
6) [Source eNB <- Target eNB] 给source eNB通知切换准备完成
target eNB发送handover request ack消息包含在step-5准备的所有资源。这些信息如下：
**Handover Request Ack** (E-RAB Admitted (E-RAB ID, Target eNB TEID), Handover Command (Target CRNTI, Target DRB ID, AS Security Algorithm of Target eNB))
 - E-RAB Admitted 12: includes i) E-RAB ID allocated by the target eNB, and ii) TEID information of X2 transport bearer through which E-RAB packets are to be forwarded to the target eNB.
 - Handover Command: Transparent Container, delivered by the target eNB to the source eNB, that contains information that UE needs to access the target eNB
     - Target C-RNTI: C-RNTI allocated by the target cell to identify UE
     - Target DRB ID: ID of DRB that the target eNB set to deliver user packets over the radio link
 - AS Security Algorithm of Target eNB: AS Security algorithm supported by the target eNB

7) [Source eNB] 为下行数据包交付建立X2传输承载
一旦接收到handover request ack消息，source eNB知道target eNB可以服务UE了。接着使用X2  target eNB TEID，source eNB开始建立X2传输承载，在切换执行阶段可以使用这个承载把下行数据包转发给target eNB。

**Handover Execution**
图6显示了X2执行阶段的详细过程。

![这里写图片描述](http://img.blog.csdn.net/20151009085052813)

8) [UE <- Source eNB] 命令切换
一旦source eNB和target eNB完成切换准备，source eNB发送handover command消息命令UE执行切换。

9) [UE] 执行切换
UE从handover command消息中获得C-RNTI和DRB ID用于target cell，并从source eNB上detach。现在UE和source eNB之间的所有数据包都中断了，切换中断时间周期开始。

10) [UE] AS安全建立
UE产生AS安全秘钥用于target eNB的无线链路。首先从source eNB的KeNB/target cell的PCI和频点信息中生成KeNB*，接着通过target eNB选择的AS安全算法来生成target eNB的AS安全秘钥(KRRCint, KRRCenc, KUPenc) 。
11) [Source eNB -> Target eNB] 通知发送或者接收的数据包序号
source eNB给target eNB发送SN Status Transfer 消息通知从哪个数据包开始发送或者接收，在这个消息中包含下行计数和上行计数，这个计数值是PDCP PDU的计数，每一个计数32bit，是由Hyper Frame Number (HFN) and PDCP Sequence Number (SN)组成。这个信息如下所示：
**SN Status Transfer** (DL Count, UL Count)
 - DL Count: Count of the first packet to send to the UE
 - UL Count: Count of the first packet to receive from the UE

在发送给target eNB SN status transfer消息后，source eNB开始转发从SGW的下行数据包到target eNB。target eNB缓存这些数据包等待UE接入完成。

12) ~ 14) [UE, Target eNB] UE接入到target eNB

12) UE检测到target eNB的同步信号并执行同步到target eNB。一旦同步完成，UE发起非竞争的随机接入。

13) target eNB给UE发送定时偏移信息（时间提前量）和UL grant。

14) UE给target eNB发送handover confirm消息包含在RRC connection reconfiguration complete 消息。现在，UE可以发送或接收来自target eNB的数据包，切换中断时间周期结束。

15) [UE - Target eNB] 在无线链路上安全通信
UE和target eNB之间无线链路上的所有rrc信令消息和用户数据包都使用AS安全秘钥安全的传输。RRC信令消息是完整性保护和加密的，用户数据包是加密的。

16) [Target eNB] 恢复发送至UE的下行数据包
一旦UE成功连接到target eNB，target eNB开始通过图6的路径发送缓存的下行数据包给UE。至于UE发送的数据包，target eNB检查上行数据包是否以正确的顺序接收，并通过图6的路径转发给SGW。

**Handover Completion**
图7描述了X2切换完成阶段的详细过程。

![这里写图片描述](http://img.blog.csdn.net/20151009085152436)

17) [Target eNB -> MME] 请求EPS承载（S1承载）路径转换
target eNB通过发送path switch request消息通知EPC（MME）UE的服务消息转换了，并请求转换EPS承载路径。

18) ~ 23) 修改EPS承载
MME转发target eNB分配的S1 Target eNB TEID给SGW。通知SGW下行S1承载已经改变了，并要求改变这个承载路径。SGW建立下行S1承载连接到target eNB。一些SGW根据UE初始attach的设置的选项在EPS会话创建时需要报告UE的服务小区是否改变。在此场景下，S-GW向P-GW发一个modify bearer request消息，让P-GW向PCRF发送报告，根据EPS  session modification procedure，指示说UE的服务小区已经发生了改变。

24) [S-GW] 修改EPS承载路径并发送EM数据包
现在下行S1承载已经修改，SGW转换下行数据包交付路径到连接target eNB的下行S1承载。首先SGW发送结束标记EM来标识到达source eNB的下行S1承载的最后一个数据包。接着SGW通过修改过的下行S1承载发送下行数据包。

25) [Target eNB] 数据包重新排序
现在target eNB从source eNB和从修改过的下行S1承载接收下行数据包。所以，应该以一定的顺序交付给UE这些数据包。首先，target eNB交付给UE source eNB转发的下行数据包，当收到EM，target eNB知道这是从X2承载的最后一个数据包，接着target eNB发送从S1承载接收到的数据包给UE。

26) [Target eNB <- MME] 通告修改过的承载路径
MME通过发送path switch request ack消息通知给target eNB，SGW已经完成了EPS承载路径的转换。接着，MME转发用于切换的安全上下文{NH Chaining Count (NCC), Next Hop (NH)}，target eNB可以在UE下次切换到另外一个小区时使用。

27) [Source eNB <- Target eNB] 通知释放UE上下文
target eNB保留{NCC, NH}并给source eNB发送UE context release消息，通知UE上下文可以释放，UE承载路径转换完成。

# IV. EPS Entity Information: Before/After X2 Handover

这章描述在X2切换前后EPS实体信息的变化。所有的信息被分类为UE ID，UE Location，安全，EPS会话/承载信息。

##  4.1 Before X2 Handover
在X2切换前，UE处于EMM-Registered and ECM/RRC-Connected状态。直到执行X2切换构成，所有的信息和初始附着/service request之后的信息相同。也就是，E-UTRAN和EPC分配的资源和存储在EPS实体中的UE上下文信息。图8列出来这些信息。

![这里写图片描述](http://img.blog.csdn.net/20151009085240097)

##  4.2 After X2 Handover
在X2切换后，UE处于EMM-Registered and ECM/RRC-Connected状态。存储在每一个EPS实体中的信息没有改变，但是UE的Location信息发生变化。在source eNB中所有的E-UTRAN资源和UE上下文都释放了，但是在target eNB中设置了。图9列出来这些信息。在图中，改变的信息被标记为蓝色。

![这里写图片描述](http://img.blog.csdn.net/20151009085318794)

# V. Closing

到目前为止我们讨论了MME和SGW都不发生改变的X2切换过程。X2切换是在没有EPC参与下由source 和target eNB来执行的。我们学习了下行数据包通过X2传输承载转发来避免数据包丢失。在第三篇文档，我们将讨论S1切换，以及S1切换和X2切换的不同之处。

# References
[1] Netmanias Technical Document, “LTE EMM Procedure 6. Handover without TAU – Part 1. Overview of Handover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6224
[2] 3GPP TS 36.423, “Evolved Universal Terrestrial Radio Access Network (E-UTRAN); X2 application protocol (X2AP)”.
[3] 3GPP TS 36.331, “Evolved Universal Terrestrial Radio Access (E-UTRA); Radio Resource Control (RRC); Protocol specification”
[4] Netmanias Technical Document, “LTE Security II: NAS and AS Security”, August 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=5903
[5] Netmanias Technical Document, “LTE Security I: LTE Security Concept and LTE Authentication”, July 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=5902
[6] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010