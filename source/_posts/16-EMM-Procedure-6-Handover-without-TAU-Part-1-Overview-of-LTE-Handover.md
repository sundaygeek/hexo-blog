---
title: 16-EMM Procedure 6. Handover without TAU - Part 1. Overview of LTE Handover
date: 2016-04-03 22:34:45
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6224

# I. Introduction

这篇文档描述EMM case-6。这个过程是当UE移动到在TAI列表中的TA的一个小区时执行的。在handover过程中，并不发起TAU过程。我们定义这种过称为“Handover without TAU”。这个过程通过接下来的三篇文档讲述。第一个文档描述LTE handover，提供基本的信息。第二篇文档描述X2 handover，第三篇文档描述intra-LTE环境下的S1 handover。

# II. Overview of LTE Handover

无线设备比有线的一个最大的好处就是它可以移动中使用服务。这种移动性可以使用户在任何位置使用该服务，不论是家里还是在路上，任何想要的时间都可以。正因为这种便利，无线用户大大超过有线用户。签约服务的用户可以在路上使用服务就是因为移动网络支持handover切换。UE可以从一个基站在不丢失数据的情况下切换到另外一个基站。这确保用户可以被无缝地服务到，不论连接到那个小区。这个文档描述UE和LTE实体需要采取什么行动来支持切换，并且简要的讲述这些行为的不同。和切换的过程如表1所示。

![这里写图片描述](http://img.blog.csdn.net/20150925133843129)

UE有一个天线可以在多个band上搜索多个频率信道。所以在检查完多个邻区之后，UE通常接入有最强信号强度的那个小区（除非因为接入限制或者拥塞控制而不允许接入）。接着随着UE的移动和遮蔽，UE当前服务小区的接收信号强度变弱了，而邻区的信号变强，发起handover。允许UE接入邻区并建立新的RRC连接。

为了达到这种目的，当UE和eNB建立RRC连接时，eNB通过一个配置消息（RRC Connection Reconfiguration）给UE指示哪些事件的接收信号强度需要报告。UE监视服务小区和邻区的接收信号强度。当其中一个事件发生，UE通过Measurement Report消息把接收信号强度报告给eNB。eNB一旦接收到这个消息，通过查看报告强度信息和邻区的负载状态决定是否发起handover。一旦决定，将执行handover到选择的目的小区。

##  2.1 Measurement

图1描述了当UE测量和报告信号轻度的测量配置和测量报告过程。

![这里写图片描述](http://img.blog.csdn.net/20150925133953323)

**(1) Measurement Configuration**
测量配置是由eNB提供给UE的，eNB指示什么类型的测量信息需要报告。eNB通过RRC connection reconfiguration消息提供给UE这些测量配置。这些信息包含如下：

 - 测量对象：提供UE需要测量的E-UTRAN小区，包括，频道号，物理小区ID-PCI，小区ID的黑名单，每一个小区的偏移值等等。
 - 报告配置：指示需要UE发送测量报告信息的触发事件
 - 测量ID：描述测量对象的ID
 - Quantity配置：指示UE测量的值
 - 测量间隔：指示UE测量邻区的间隔

在同频邻区测量情况下，UE可以不使用测量间隔(gap)来测量邻区。
但是在异频邻区测量时，UE应该首先同步到邻区的频点，然后在上下行idle周期内使用测量间隔gap来测量信号强度。

**(2) Measurement Report Triggering**
UE测量服务小区和邻区的信号强度。接着UE把测量结果报告给eNB（周期性的，或者在满足测量配置中设定的报告门限时触发测量事件）。E-UTRAN的报告门限包括事件A1, A2, A3, A4 and A5，在inter-RAT测量报告中包括事件 B1 and B2。Event A3通常用来触发切换。图2显示了由事件A3触发切换的实例。表2提供了事件A3使用的符号。

**Event A3**
当邻区的信号强度（信号强度和偏移，MNbr = Mn + Ofn + Ocn）比UE服务小区的信号强度（信号强度和偏移，MSer = Ms + Ofs + Ocs）高时，并且这个差额比A3偏移 (Off)还要大时，事件A3触发，UE给eNB报告测量结果。迟滞(Hys)是服务小区和目标小区越区切换界限的值。在A3触发的时候并且A3触发判决持续指定的TTT周期，eNB决定触发切换。

![这里写图片描述](http://img.blog.csdn.net/20150925134044972)

Table 2. Definition of the Symbols Represented in E-UTRA Measurement

Symbol | Definition
------|--------
Mn |Measurement result of the neighbor cell
Ms |Measurement result of the serving cell
Hys |Hysteresis parameter for Event A3
Off |Offset parameter of Event A3
Ofn |Frequency specific offset of the frequency of the neighbor cell
Ocn |Cell specific offset of the neighbor cell
Ofs| Frequency specific offset of the serving frequency
Ocs |Cell specific offset of the serving cell


##  2.2 Handover Decision
当事件A3报告时，eNB决定执行什么类型的切换到哪一个目标小区，接着发起切换过程。切换可以分类为不同的类型，但是他们的分类遵循以下的目的：

**(1) Handover分类1: EPC实体是否发生变化**
切换可以分类为不同的类型--同频LTE，异频LTE，inter-RAT切换，这些根据UE连接的EPC实体在切换前后是否发生变化。

**同频LTE切换**
▪ Intra-MME/S-GW Handover: 在切换前后UE服务的MME和SGW是否发生变化

**异频LTE切换:** 在切换前后UE服务的MME和/或SGW发生了变化
▪ Inter-MME Handover: 在切换后，UE服务MME发生变化，而服务的SGW保持不变
▪ Inter-S-GW Handover: 在切换后，UE服务SGW发生变化，而服务的MME保持不变
▪ Inter-MME/S-GW Handover: 在切换后，UE服务MME和SGW都发生了变化

**Inter-RAT Handover:** 在不同无线接入技术之间的切换
▪ UTRAN to E-UTRAN
▪ E-UTRAN to UTRAN, etc.

**(2) Handover 分类2: 是否有EPC实体涉及到**
根据是否有EPC实体涉及到在源eNB和目标eNB之间准备和执行切换，LTE切换可以分为使用X2接口的X2切换和使用S1接口的S1切换。图3描绘了随着UE的移动而触发的两种不同类型的切换。图4显示了源eNB是如何决定是哪种类型的切换，X2或者S1。

**X2 Handover**
X2接口连接两个eNB。如果在两个服务小区的eNB和目标小区的eNB之间存在X2连接，并且X2连接可用于切换，则发起X2切换。一旦切换完成，两个eNB相互通信来控制切换，并没有MME的介入。

**S1 Handover**
S1接口连接eNB和EPC。如果i）在源eNB和目标eNB之间没有X2连接，ii）有X2连接，但是这个连接不允许用于切换，iii）在源小区和目标小区之间切换准备失败，则发起S1切换。一旦切换完成，源eNB通过MME和目标eNB通信控制切换。

![这里写图片描述](http://img.blog.csdn.net/20150925134242995)
![这里写图片描述](http://img.blog.csdn.net/20150925134252671)

##  2.3 Handover Procedure
根据接收到的测量配置，UE把测量结果报告给eNB。一旦eNB决定，切换则发起。切换过程包含三个阶段--准备，执行，完成阶段。

**(1) Handover Preparation Phase**
在这个阶段，源eNB和目标eNB准备切换。对X2切换，通过X2信令两个eNB直接进行通信，并且不需要MME介入情况下执行。对S1切换，通过S1信令，需要MME介入。
源eNB发送用户UE上下文给目标eNB来检查目标eNB是否有能力提供满意的服务质量。如果可以，目标eNB建立传输承载（下行数据包转发承载）用于数据转发。接着分配C-RNTI（用于UE接入这个eNB），并把这个值转发给源eNB。这就完成了准备阶段。这时候对X2切换，下行数据包转发承载是一个连接两个eNB的直传隧道，对S1切换，则是连接三个实体的非直传隧道。图5标书了这个阶段的数据包转发路径，UL/DL承载数据交付路径，控制信息交付路径，和下行数据包转发路径。

![这里写图片描述](http://img.blog.csdn.net/20150925134325580)

**(2) Handover Execution Phase**
在这个阶段，执行切换。UE从源eNB上释放无线连接，并连接到目标eNB，接入到新小区。一旦用于两个eNB之间数据包转发的资源(i.e. a DL packet forwarding bearer)分配完成，并且在准备阶段目标eNB为UE分配了新资源(i.e. a DRB, DL S1 bearer, C-RNTI, etc.)，则这两个eNB准备好切换了。接着源eNB通过发送handover command消息命令UE执行切换。在切换执行阶段，UE使用在切换准备阶段目标eNB分配的C-RNTI。这样可以使UE更快的接入目标eNB。一旦到达目标eNB，下行数据包通过转发承载转发给目标eNB，并缓存直到UE完成到目标eNB的接入。这确保了没有数据包丢失。从UE的上行数据包不转发直到UE成功的接入目标eNB。一旦UE完成对目标eNB的无线接入，上行数据立即通过目标eNB转发至SGW。图6描述了在切换执行阶段下行数据交付路径，和通过目标eNB上行数据交付路径。

![这里写图片描述](http://img.blog.csdn.net/20150925134405084)

**(3) Handover Completion Phase**
一旦UE完成对目标eNB的无线接入，UE承载路径 (DL S1 bearer)连接到目标eNB上而不是源eNB上。一旦这个路径转换完成，在切换执行阶段使用的转发承载被释放。在图7中，一旦完成切换完成阶段，上下行数据都使用新承载来交付的。

![这里写图片描述](http://img.blog.csdn.net/20150925134441009)

##  2.4 切换中断时间
在切换准备阶段，网络实体在执行切换时提前分配资源来确保没有下行数据包丢失。但是实际切换中，切换中断时间是不可避免的。在这个时间内（UE从源eNB释放无线资源到重新连接上新小区），数据包时不能在UE和小区之间交付的。图8显示了X2切换时切换中断时间的实例。如果中断时间持续过长，无缝服务将不支持，用户要承受差的服务质量。
切换中断时间包括：
❶ 下行同步到目标eNB的时间
❷ RACH等待时间
❸ 发送专用RACH preamble请求上行资源的时间
❹ 目标eNB检测到preamble 并处理的时间
❺ 准备RACH Response 的时间
❻ 解码RACH Response 的时间
❼ 通知UE已经完成到目标eNB切换的时间
❽ 在完成切换阶段获得目标eNB确认的时间

![这里写图片描述](http://img.blog.csdn.net/20150925134517615)

##  2.5 Mobility Robustness Optimization移动性鲁棒优化
有时候，在切换执行阶段在UE完成接入到目标小区之前UE从服务小区或者目标小区接收到的信号不足够强将会导致无线链路失败RLF。RLF引起的因素有很多，可能和切换有关，也可能没关。如果是由切换执行时间相关的原因，切换发起的太早或者太晚都会引起RLF。如果在执行切换时RLF发生，UE可能会执行RRC重建过程，并连接到服务小区/目标小区或者另外一个小区（错误的小区）。RLF也和小区覆盖有关。所以，3GPP标准定义了移动鲁棒性优化（Mobility Robustness Optimization (MRO)）来帮助检测哪种类型的接入失败，并增强切换的鲁棒性。MRO超出本片文档的范围，将可能在LTE SON自组网文档中介绍。在下一个文档，我们将讨论没有RLF的成功切换。

# III. Closing
这篇文档，讨论了LTE切换的概述，讨论了小区测量，不同类型的切换和切换阶段。接下来的两篇文档将继续深入讨论X2和S1切换。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] 3GPP TS 36.300, “Evolved Universal Terrestrial Radio Access (E-UTRA) and Evolved Universal Terrestrial Radio Access Network (E-UTRAN); Overall description”
[3] 3GPP TS 36.331, “Evolved Universal Terrestrial Radio Access (E-UTRA); Radio Resource Control (RRC); Protocol specification”
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010





