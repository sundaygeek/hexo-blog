---
title: 18-EMM Procedure 6. Handover without TAU - Part 3. S1 Handover
date: 2018-04-03 22:35:13
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6286

# I. Introduction

之前的文档，我们讨论了X2切换，这篇文档我们关注在EPC干预下的S1切换。这里，我们假设source和target eNB连接在同一个MME/SGW，并且位于UE的TAI列表中的同一个TA下。第二章我们描述S1切换的概念，第三章我们详细描述S1切换，最后第四章我们总结在S1切换前后EPS实体信息的变化。

# II. Concept of S1 Handover
##  2.1 S1协议栈
S1切换是通过S1接口source和target eNB之间执行的。在控制面eNB和MME通过S1AP信令通信，在用户面eNB和SGW通过GTP隧道通信。图1显示控制面和用户的S1接口上的协议栈。

![这里写图片描述](http://img.blog.csdn.net/20151010104116557)

当安装一个新eNB时，就需要在eNB和MME之间执行S1 setup过程。eNB通过发送S1 setup request(eNB ID, eNB Name,TAC)给MME通知eNB配置信息。在MME中也会使用相应的** MME容量参数** 用于MME间的负载均衡。这个值被标记为权值，表示每一个MME处理UE连接的相对容量。eNB连接不止一个MME，当选择一个MME建立新的UE连接时使用这个值。eNB和EPC之间的UE连接如下：在控制面，eNB和MME之间每一个用户的信令都是通过S1AP信令连接提供的，并使用{eNB UES1AP ID, MME UE S1AP ID}标识，在用户面，eNB和SGW之间的每一个用户的S1承载都是通过GTP隧道来提供，并使用{DL S1 TEID (S1 eNB TEID), UL S1 TEID (S1 SGW TEID)}标识。

##  2.2 和切换相关的S1AP过程和消息
表1和2是在GPP TS 36.413中S1AP的基本过程，包含non-UE和UE相关的过程。这个文档是和切换相关的，所以这里只讨论UE相关的过程。表1列出了S1切换相关的S1AP过程和他们相关的S1AP消息。

![这里写图片描述](http://img.blog.csdn.net/20151010104218858)

列在上表中的这些S1AP消息在下面详细介绍：
 - ** Handover Required message** : 这个消息用于切换准备阶段，由eNB发送给MME，包含target eNB的信息和source cell的无线资源。
 - ** Handover Request message** : 这个消息在切换准备阶段使用，由MME发送到target eNB，包含UE上下文信息。
 - ** Handover Request Acknowledge message** : 这个消息是在切换准备阶段使用的，它是当在target eNB中成功为UE分配资源时由target eNB发送给MME。target eNB分配DL S1 TEID供S1承载在切换后使用，并分配DL S1 TEID供S1承载(indirect tunnel)用于在切换时下行数据包的传送，包含在这个消息中转发他们。
 - ** handover command message** ：这个消息用于切换准备阶段，是由MME发送给source eNB。它包含了UE接入target eNB需要的信息（e.g. Target C-RNTI, Target eNB AS Security algorithm, DRB ID, etc.），并包含UL S1 TEID供SGW在切换时用于下行数据包传送。
 - ** eNB status transfer message** ：这个消息是在切换执行阶段使用，由source eNB发送给MME。指示target eNB应该从哪个数据包开始接收或者发送。
 - ** MME Status Transfer message** : 这个消息是在切换执行阶段使用，是由MME发送给target eNB。指示target eNB应该从哪个数据包开始接收或者发送。
 - ** Handover Notify message** : 这个消息在切换完成阶段使用，是由target eNB发送给MME，指示UE已经完成到target eNB的切换。
 - ** UE Context Release Command message** : 这个消息用在切换完成阶段，是由MME发送给source eNB请求UE上下文的释放。
 - ** UE Context Release Complete message** : 这个消息是在切换完成阶段使用，是由source eNB发送给MME指示UE上下文已经释放了。

##  2.3 S1 Handover Procedure at a Glance

像之前文档看到的，S1切换过程包括准备，执行，完成阶段。在我们详细介绍之前，我们简要预览一下S1切换过程。

![这里写图片描述](http://img.blog.csdn.net/20151010104310786)

图2描述了在切换前/中/后需要的过程。方便起见，SGW和PGW被标记为SAE-GW，source和target eNB被标记为SeNB和TeNB。

** before S1 handover** 
在上图中，UE是通过eNB A获得服务的。当UE检测到测量事件时，UE发送measurement report 给eNB A。

** S1 handover preparation** 
source eNB基于自己保留的邻区列表信息和在measurement report 消息中包含的邻区信号强度信息选择target eNB作为切换目的地。接着，source eNB意识到通过X2连接切换到target eNB是不可能的，所以决定执行S1切换，并通过MME执行。所有的eNB都是通过S1AP信令和MME通信。这时，target eNB提前分配资源来确保source eNB提供的服务在target eNB同样可用。MME提供给source eNB所有的UE接入target cell需要的所有信息。

期间，target eNB和SGW分配创建indirect tunnel需要的资源，通过这个indirect tunnel，到达source eNB的下行数据转发给SGW，最终发送给target eNB。如下所示：

 - source eNB发送target eNB所需要的信息给MME， 包含在handover required消息中。（1）
 - MME发送handover request消息给target eNB，包含target eNB需要的AS安全信息来创建AS安全基础秘钥和UE上下文。
 - target eNB（2）
     - 使用从MME获得的S1 S-GW TEID建立上行S1承载，通过这个承载在切换后可以转发上行数据包。并为下行S1承载分配S1 target eNB TEID。（3）
     - 为连接SGW和target eNB的indirect tunnel分配S1 target eNB TEID。
     - 配置handover command消息，包含UE接入target cell所需要的所有的信息。
     - 把这些信息包含在handover request ack发送给MME。（4）
 - MME一旦接收到这个消息，在create indirect data forwarding tunnel request消息中包含target eNB为indirect tunnel分配的S1 target eNB TEID，并把这个消息发送给SGW。（5）
 - SGW
     - 创建indirect tunnel连接target eNB（6）
     - 分配S1 S-GW TEID用于source eNB和SGW之间的indirect tunnel的创建，并通过Create Indirect Data Forwarding Tunnel Response消息发送给MME。
 - MME包含i）SGW为indirect tunnel分配的S1 S-GW TEID，ii）包含UE接入target cell所需的信息，包含在handover command消息发送给source eNB。
 - 接着，source eNB创建连接SGW的indirect tunnel。

通过第8和第6步，连接source eNB，SGW，target eNB三个实体的indirect tunnel创建成功了。

** S1 handover execution** 
现在两个eNB准备好切换了，是时候命令UE执行了。

 - source eNB
     - 通过发送handover command消息包含UE接入target cell需要的信息命令UE执行切换到target cell（1）
     - 发送eNB status transfer消息通知MME，eNB应该从哪个上行/下行数据包开始接收/发送。（2）
     - 通过indirect tunnel发送从SGW接收到的下行数据包给target eNB。（4）
 - MME通过发送MME status transfer消息通知target eNB，它应该从哪个UL/DL数据包开始发送/接收。（3）
 - UE从source eNB断开，接入到target eNB。（5）
 - 一旦UE成功接入target eNB，UE就立即有能力发送或接收数据包了。（6）

** S1 handover completion** 
因为MME已经知道UE执行切换，不像X2切换，target eNB不需要MME进行路径修改。一旦UE连接到target eNB，target eNB给MME发送handover notify消息指示UE已经完成切换。

 - 一旦UE连接，target eNB给MME发送handover notify通知切换完成（1）
 - 接着MME请求SGW做S1承载修改。SGW修改下行S1承载路径连接target eNB。（2）（3）
 - SGW改变承载路径：如下
     - SGW通过连接source eNB的下行S1承载发送end marker（EM）停止下行数据包传输。
     - 接着SGW创建连接target eNB的下行S1承载，并恢复下行数据包传输给target eNB。

 - target eNB给UE发送下行数据包如下：
     - target eNB在EM标记数据包到达之前通过indirect tunnel发送下行数据包
     - 一旦EM标记数据包到达，target eNB通过新路径给UE发送数据包。

 - MME：
     - MME通过发送UE context release command消息请求source eNB释放S1承载资源和UE上下文。（4）
     - MME通过发送delete indirect data forwarding tunnel request消息通过SGW释放和indirect tunnel相关的资源。（5）

** after S1 handover** 
UE现在可以通过eNB B提供服务。

##  2.4 在S1切换前后UE状态和连接信息
图3描述了在S1切换前后控制面用户面的连接建立情况，和UE/MME的状态。

** before S1 handover** 
UE在EMM-REgistered和ECM/RRC-connected状态，并保持着E-UTRAN和EPC分配的所有资源。

** during S1 handover** 
即使在切换阶段，NAS层的UE状态一直保持不变。source eNB和target eNB都通过S1信令连接到MME上。他们也通过indirect tunnel连接到SGW上。在图3中，step-2展示了在切换执行阶段切换中断时的连接和状态。在这个阶段，没有激活的无线连接，但是UE仍保持连接状态。

** after S1 handover** 
UE保持在EMM-REgistered和ECM/RRC-connected状态。在用户面，E_RAB（DRB+S1承载）转到连接新eNB的路径上，在控制面建立了新的RRC连接。

![这里写图片描述](http://img.blog.csdn.net/20151010104408319)

# III. Procedure of S1 Handover

现在我们详细介绍S1切换过程。图4描述了在S1切换之前的EPS承载和信令连接，并包括S1切换准备阶段详细过程。
![这里写图片描述](http://img.blog.csdn.net/20151010104449020)

** before handover** 
** 1）【UE->eNB】measurement report** 
一旦测量事件触发，UE测量邻区的信号强度，并给服务小区发送measurement report 消息。

** handover preparation** 
** 2）【source eNB】handover decision** 
source eNB根据measurement report消息包含的信息和source eNB自己保留的邻区列表信息中选择target eNB。并且发现在两个eNB之间没有有效X2连接可用，source eNB决定执行S1切换。

** 3）【source eNB->MME】 requesting handover** 
source eNB发送handover required消息给MME，请求切换到target eNB。这个消息包含如下：
Handover Required (Handover Type, Target eNB ID, Source to Target Transparent Container)

 - Handover Required (Handover Type, Target eNB ID, Source to Target Transparent Container)
 - Target eNB ID: 包含Target Global eNB ID and Selected TAI information
 - Source to Target Transparent Container: is used when forwarding the radio-related information of the source cell to the target cell transparently through EPC (MME)

** 4) [MME] 产生安全上下文转发给target eNB** 
MME生成安全上下文{NCC,NH}, target eNB可用产生AS安全基础秘钥。NCC从初始NCC值开始加1，NH是从初始NH值和Kasme值产生的。
![这里写图片描述](http://img.blog.csdn.net/20151013092154440)

** 5）【target eNB <-MME】请求target eNB切换** 
MME给target eNB发送handover request消息，代表 source eNB请求切换。这个消息如下：
Handover Request (UE-AMBR, E-RAB to be setup (E-RAB ID, QCI, ARP, S1 S-GW TEID), Source to Target Transparent Container, UE Security Capability, Security Context)

 - Handover Request (UE-AMBR, E-RAB to be setup (E-RAB ID, QCI, ARP, S1 S-GW TEID), Source to TargetTransparent Container, UE Security Capability, Security Context)
 - E-RAB to be setup: UE’s E-RAB information stored at Source eNB. Includes E-RAB ID, QoS parameters, UL S1 bearer information
 - Source to Target Transparent Container: is used when forwarding the radio-related information of the source cell (e.g. UE radio access capability, RRC configuration Info, etc.) to the target cell transparently through EPC (MME).
 - UE Security Capability: security algorithms supported by UE (encryption and integrity algorithm)
 - Security Context: includes {NCC, NH} to be used when Target eNB derives the AS Security base key, KeNB*

** 6) [Target eNB] 准备S1 handover** 
一旦接收到handover request消息，target eNB开始准备切换为UE提供无缝服务
** i)新的S1承载资源分配** ：target eNB基于要建立的E-RAB信息，检查source eNB提供的QoS是否在target eNB中可用。如果可用，target eNB使用存储在source eNB中的上行S1承载信息（S1 SGW TEID）建立上行S1承载连接SGW。接着分配S1 target eNB TEID准备下行S1承载。

** ii）indirect tunnel资源分配** ：在UE执行S1切换时，应该有一个从source eNB到target eNB的indirect tunnel。所以，target eNB分配S1 target eNB TEID，SGW可以建立indirect tunnel连接target eNB。

** iii）为UE在无线链路上分配资源** ：基于E-RAB的QoS信息，target eNB为UE在无线链路上保留RRC资源，并分配C-RNTI。

** iv）Kenb*产生** ：使用从MME中接收到的安全上下文信息（NCC1，NH1）来产生Kenb*，接着获得AS安全秘钥(KRRCint, KRRCenc, KUpenc)。接着，当UE连接到target eNB是，UE和target eNB可以使用这些秘钥来安全的通信。图5显示了Kenb *是怎么产生的。我们可以看到Kenb *从NH1，PCI，频点生成。
![tunnel connecting to the target eNB.](http://img.blog.csdn.net/20151010104536396)
** 7）【target eNB->MME】通知MME准备完毕** 
target eNB给MME发送handover request ack消息，消息中包含了step-6中的所有资源。这些消息如下所示：
Handover Request Ack (E-RAB Admitted(E-RAB ID, S1 Target eNB TEID, DL S1 Target eNB TEID), Handover Command (Target C-RNTI, Target DRB ID, AS Security Algorithm of Target eNB))
 - E-RAB Admitted
     - ** E-RAB ID** : E-RAB ID allocated by the target eNB
     - ** S1 Target eNB TEID** : DL S1 TEID that the target eNB allocated to S-GW for establishment of S1 bearer connecting to itself.
     - ** DL S1 Target eNB TEID** : DL S1 TEID that the target eNB allocated for establishment of an indirect tunnel for handover through which to deliver DL packets.

 - ** Handover Command:**  Transparent Container, delivered by the target eNB to the source eNB, that contains the radio information of the target cell that UE needs to access the target eNB.
     - Target C-RNTI: C-RNTI allocated by the target cell to identify UE.
     - Target DRB ID: ID of DRB that the target eNB set to deliver user packets over the radio link.
     - Target DRB ID: ID of DRB that the target eNB set to deliver user packets over the radio link.

** 8）【MME->SGW】请求S1承载创建用于下行数据包传输** 
MME给SGW发送Create Indirect Data Forwarding Tunnel Request消息，请求创建indirect tunnel用于交付下行数据包。这个消息包括GTP TEID（S1 Target eNB TEID，target eNB分配的）

** 9）【MME<-SGW】通知MME S1承载创建完成** 
SGW一旦接收到Create Indirect Data Forwarding Tunnel Request消息，创建一个连接target eNB的indirect tunnel。接着分配S1 SGW TEID，通过Create Indirect Data Forwarding Tunnel Response消息转发给MME，source eNB可以使用这个TEID穿件indirect tunnel连接SGW。

** 10）【source eNB<-MME】通知完成切换** 
MME发送给source eNB handover command消息包含i)S1 SGW TEID（在step-9从SGW接收到）ii）包含包含handover command消息（在step-7从target eNB接收到的）
source eNB从handover command消息中知道target eNB和EPC已经准备UE切换了。

** handover execution** 
图6描述了S1切换的执行阶段。
![这里写图片描述](http://img.blog.csdn.net/20151010104638181)

** 11）【UE<-source eNB】命令UE执行切换** 
一旦source eNB准备好切换，source eNB发送handover command消息命令UE执行切换。这个消息是包含在RRC connection reconfiguration消息中的。

** 12）【UE】执行切换** 
一旦UE从handover command消息中获得C-RNTI和DRB ID，并从source eNB detach。现在UE和source eNB之间的所有的数据包交换都停止了，切换中断时间开始了。

** 13）【UE】AS安全建立** 
UE产生AS安全秘钥用于target eNB的无线链路。首先产生Kenb*，接着使用target eNB选择的AS安全算法产生AS安全秘钥 (KRRCint, KRRCenc, KUPenc)。
** 14）~15）【source eNB->MME.MME->target eNB】通知从哪个数据包开始发送/接收** 
source eNB发送eNB status transfer 消息，包含DL count和UL count给MME。接着MME通过MME status transfer消息包含相同的信息给target eNB。这是为了让target eNB知道从哪个数据包开始发送/接收数据包。这里这个count值是PDCP PDU计数，每一个count是32bit值，包含HFN超帧号和PDCP序列号SN。这个消息如下：
eNB Status Transfer (DL Count, UL Count)
 DL Count: Count of the first packet to send to the UE
 UL Count: Count of the first packet to receive from the UE
在发送eNB status transfer消息之后，source eNB开始通过indirect tunnel转发从SGW到达的下行数据包给target eNB。target eNB缓存这些数据包，知道UE成功接入到target eNB。
** 16）-18）【UE，target eNB】UE接入到target eNB** 
16）UE检测到target eNB的同步信号并执行同步到target eNB。一旦同步完成，UE发起非竞争的随即接入。17）target eNB给UE发送时间偏移信息和上行授权。18）UE给target eNB发送handover confirm消息包含在RRC connection reconfiguration complete消息中。
现在UE可以从target eNb接收或发送数据包了。切换中断时间结束。
** 19）【UE~target eNB】无线链路上的安全通信** 
UE和target eNB之间的所有的RRC信令消息和用户数据包都是使用AS安全秘钥安全传输的。rrc信令消息是完整性保护和加密的，用户数据是加密的。
** 20）【target eNB】恢复下行数据包交付给UE** 
随着UE成功连接到target eNB，target eNB通过下面的路径恢复发送缓冲的下行数据包给UE。
S5承载( PGW到SGW)----S1承载（SGW到source eNB）-----S1承载（source eNB到SGW）----S1承载（SGW到target eNB）---DRB（target eNB到UE）
对于UE发送的数据包，target eNB检查是否以正确的顺序接收到上行数据包，接着通过下面的路径转发给SGW。
DRB（UE到target eNB）---S1承载（target eNB到SGW）---S5承载（SGW到PGW）
** handover completion** 
图7描述了S1切换结束阶段的过程。
![这里写图片描述](http://img.blog.csdn.net/20151010104733471)
** 21）【target eNB->MME】请求EPS承载路径转换** 
一旦UE成功接入，target eNB通过发送handover notify消息给MME通知UE已经成功完成了S1切换，这个消息中包含ECGI和TAI。
** 22）~27）修改EPS承载** 
MME转发由target eNB分配的S1 target eNB TEID给SGW（通过发送modify bearer request消息）。通过这种方式请求SGW修改承载路径。接着SGW建立下行S1承载连接target eNB。一些SGW，根据UE初始附着时的选项设置，需要报告UE的服务小区是否发生变化。就需要SGW发送modify bearer request消息给PGW（PGW报告给PCRF），根据EPS会话修改过程，表示UE服务小区发生变化了。
** 28）~29）【SGW】修改S1承载路径** 
SGW转换下行数据包转发路径为连接target eNB的下行S1承载。首先SGW发送end marker（EM）来指示发送给source eNB的最后一个数据包。接着它通过连接target eNB的S1承载来发送下行数据包。
** 30）【target eNB】数据包重新排序** 
现在，target eNB接收到从source eNB转发过来的下行数据包，和通过新修改的路径的下行数据包。所以应该以正确的顺序交付给UE。首先target eNB转发从indirect tunnel接收到的数据包，接着当EM到达时，就是知道从indirect tunnel的最后一个数据包，接着发送从新修改的路径上接收到的下行数据包给UE。
** 31）~32）【source eNB<->MME】释放存储在source eNB中的UE上下文和S1资源** 
MME通知source eNB，source eNB可以释放indirect tunnel和S1资源，以及UE上下文了（通过发送UE context release command消息）。
** 33）~34）【MME<->SGW】MME给SGW发送delete indirect data forwarding tunnel**  
request消息请求释放indirect tunnel。一旦接收到这个请求，SGW释放indirect tunnel，并发送delete indirect data forwarding tunnel response消息通知MME释放完成。
# IV. EPS entity information：before/after S1 handover
在intra-LTE环境中，在S1切换前后存储在EPS实体中的信息和X2相同、
 - 切换相关的As安全上下文会根据切换的类型不同俄日变化（S1 or X2）。切换安全在本篇文档范围之外。
 - 在切换过程中，EPS实体中存储的信息十分相同，但是也会根据切换类型而不同。

# V. closing

目前为止，我们讨论了在SGW和MME都不发生变化时的S1切换过程。不想X2切换，在S1切换中，EPS知道UE的切换。EPC从切换准备阶段参与到切换过程。它和source eNB，target eNb协作共同完成切换。
在EMM case-6我们讨论了LTE 切换。接下来对我们将讨论小区重选。

# References
[1] Netmanias Technical Document, “LTE EMM Procedure 6 – Part 2. X2 Handover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6257
[2] 3GPP TS 36.413, “Evolved Universal Terrestrial Radio Access Network (E-UTRAN); S1 Application Protocol (S1AP)”
[3] Netmanias Technical Document, “LTE EMM Procedure 6 – Part 1. Overview of LTE Handover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6224
[4] 3GPP TS 36.331, “Evolved Universal Terrestrial Radio Access (E-UTRA); Radio Resource Control (RRC); Protocol specification”
[5] Netmanias Technical Document, “LTE Security II: NAS and AS Security”, August 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=5903
[6] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[7] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010