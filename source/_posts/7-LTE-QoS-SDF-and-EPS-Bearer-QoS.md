---
title: '7-LTE QoS: SDF and EPS Bearer QoS'
date: 2016-04-03 22:18:31
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=5908

# I. Introduction

SDF：Service Data Flow

LTE服务提供商应该能够根据用户的不同订阅等级来定制不同的QoS的服务需求。所以，为了能够根据不同用户流量分配无线和网络资源，并为了合适的管理这些资源，服务提供商需要能感知每一个用户的订阅级别和能感知请求的服务类型。

为了这种原因，LTE网络首先根据通过SDF提供服务的类型来把用户数据流分为不同的SDFs（具有不同QoS），然后对每一个SDF应用不同的QoS规则。因为SDF在LTE网络中是通过EPS承载来传送的，所以EPS承载QoS以保持SDF QoS的方式来控制。

在这个文档中，适用于SDF的QoS机制和EPS承载将解释为LTE中的QoS机制，并且举例说明他们是怎么工作的。这个文档组织如下：第二章定义SDF和EPS承载。第三章描述适用于SDF和EPS承载的QoS参数。第四章介绍QoS提供和QoS实施，通过实体的QoS参数是怎么提供和在哪里实施的。在第五章，将举例说明LTE QoS是怎么工作的。

# II. SDF and EPS Bearer

图1描述了SDF和EPS承载，和他们之间的关系。在LTE网络中，用户数据（IP数据包）被分为SDF数据流和EPS承载数据流。一个SDF是和用户正在使用的服务相关联的一组IP数据流，而EPS承载是具有相同QoS级别的聚合的SDFs数据流。

![这里写图片描述](http://img.blog.csdn.net/20150912153615135)

SDF和EPS承载是通过使用不同的数据包过滤器匹配IP数据流来检测的（SDF template用于SDF，traffic flow template TFT用于EPS承载）。数据包过滤器是由网络运营商根据他们的策略预先配置的。他们每一个都有由五元组组成（源IP地址，目的IP地址，源端口，目的端口，协议ID）。

换种说话，在ＬＴＥ网络中，具有相同服务特征（匹配SDF template数据包过滤器）的IP数据流被指定为一个SDF。匹配TFT数据包过滤器的SDFs被映射为EPS承载，最后交付给UE。具有相同QoS级别的SDFs被聚合在一起通过一个EPS承载传送，不同的QoS级别的SDF通过不同的EPS承载传送。

**SDF**

使用不同服务或应用的用户数据流具有不同的QoS。一个SDF是一个IP数据流或者是根据服务类型分类的用户IP数据流的聚合。不同的SDF由不同的QoS级别，因此一个SDF作为一个单元服务的，这个单元的QoS规则是按照LTE中PCC过程得到的。在图1中，到UE的IP数据流使用SDF template按照他们的服务类型被分类成不同的SDF。接着相应的QoS规则（例如，优先级，带宽控制等）在数据传输到ＵＥ之前应用到SDF上。QoS是当SDF在ＬＴＥ网络传输的时候由EPS承载提供的，每一个SDF被PGW映射到一个EPS承载来满足它的QoS需求，并交付给ＵＥ．

**EPS承载**

有两种类型的EPS承载：默认和专用的。当UE附着到LTE网络时，分配一个IP地址用于连接PDN，同时建立一个默认EPS承载。当用户通过默认承载试图接入需要高QoS（当前的默认承载满足不了这个QoS）时，就按需建立一个专用承载。专用承载建立的QoS是不同于当前已经存在的承载上的QoS。UE可以连接不止一个PDN，包含一个必须的默认EPS承载和零到多个可选的专用EPS承载。UE拥有的EPS承载的数目不超过11个。

一旦在UE初始附着到网络时建立默认承载，这个承载会一直持续，即使没有服务在使用，直到UE从网络去附着。每一个PDN上都有一个默认承载。当UE初始附着到网络，网络MME需要关于怎样建立默认承载的信息，比如使用哪个QoS，需要连接哪个PDN。这些信息已经作为签约信息提供了HSS。所以，MME只需要下载这些签约信息即可（默认APN，EPS签约QoS信息等等），并基于这个APN选择一个PGW连接到PDN，并且基于签约QoS配置信息激活和这个PDN相关的默认承载。

在图1中的SDF和EPS承载
图1展示了当通过EPS下行IP数据流交付给UE时的SDF和EPS承载。通过PDN IP数据流到达PGW并使用SDF templates过滤成不同的SDF。在这个图中，IP数据流1/2/3被过滤成SDF1/2/3，相应的IP数据流4/5倍过滤成SDF4。对每一个SDF适用不同的QoS策略，接着映射到通过使用TFT分类的EPS承载上。SDF1/2映射到默认承载，而SDF3/4映射到专用承载，接着都传输都ＵＥ。一旦到达UE，IP数据被发送到相应的目的应用。

在图1中显示的EPS承载和SDF列出来表1中。在IP连接接入网（IP Connectivity Access NetworksI P-CAN）范围外的在3gpp标准中。这个文档只讨论EPS。所以这里讨论的所有的IP_CAN都是指EPS承载。

![这里写图片描述](http://img.blog.csdn.net/20150913083312809)

# III. QoS Parameters of SDF and EPS Bearer

在第二章学习了使用数据包过滤器把用户承载分为SDF或EPS承载。SDF QoS和EPS承载QoS对应的应用于SDF和EPS承载。在第三章我们学习SDF和EPS承载的QoS，以及他们之间的关系。

在LTE网络中，QoS在服务层面和承载层面上定义。SDF QoS参数一个服务层面的QoS参数，而EPS承载QoS参数是承载层面的QoS参数。服务层面和承载层面也被称为SDF层面和SDF聚合层面。一个SDF聚合就是一组具有相同QCI（QoS级别标识QoS Class Identifier）和ARP（分配和保留优先级Allocation and Retention Priority）的SDF，这些SDF属于同一个EPS承载。QCI和ARP都是基本的QoS参数，适用于所有的SDF和EPS承载。QCI特别重要，因为它作为一个参考标识SDF和EPS承载的性能特征。除了这两个基本的参数，还有其他的QoS参数，例如GBR、MBR、AMBR，这些参数指定SDF和EPS承载的 带宽/比特速率特征。SDF和EPS承载的参数如下所示：

 - SDF QoS参数：QCI、ARP、GBR、MBR
 - EPS承载QoS参数：QCI、ARP、GBR、MBR、APN-AMBR、UE-AMBR

**SDF QoS参数**

QCI和ARP使用于所有的SDF。QCI是1-9的整数，标识IP数据包9种不同的QoS性能特征，例如资源类型（GBR或NON-GBR）优先级（1-9）数据包延迟预算（50-300ms）数据包错误速率（10-2 ~ 10-6）。
MBR和GBR（最大比特速率和保证比特速率Maximum Bit Rate 和Guaranteed Bit Rate ）都是SDFQoS参数，他们用来标识SDF带宽/比特速率。MBR指定SDF的最大比特速率。如果网络流量不拥挤，则以SDF传输的用户流量可以以最大MBR传输。而，GBR是SDF保证的比特速率。这意味着无论什么情况，SDF能保证一个特定的GBR。所以即使网络流量是拥挤的，通过SDF传输的用户流量也至少能按照保证的GBR传输。

有两种类型的SDF，GBR SDF和non-GBR SDF。对于GBR SDF，根据QCI指定的资源类型分配专用的网络资源。但是，对于non-GBR SDF，不分配专用的网络资源。GBR SDF分配一个GBR和MBR，而non-GBR SDF只分配一个MBR。这两种SDF的QoS参数如下所示：

 - GBR SDF QoS参数：QCI、ARP、GBR（UL/DL）、MBR（UL/DL）
 - non-GBR SDF QoS参数：QCI、ARP、MBR（UL/DL）

匹配TFT过滤器的SDF在PGW中映射为一个EPS承载，并通过这个映射的EPS承载交付给ＵＥ．具有相同QCI和ARP的SDF聚合被映射到一个EPS承载上。

**EPS承载QoS参数**

QCI和ARP使用所有的EPS承载。EPS承载根据不同QCI的资源类型被分为GBR承载和non-GBR承载。默认承载必须是non-GBR，而专用承载可以是GBR，也可以是non-GBR。
除了QCI和ARP，EPS承载还有其他的QoS参数，MBR和GBR用来指示EPS承载的带宽/比特速率，AMBR（聚合最大比特速率Aggregated Maximum Bit Rate）指示多个EPS承载的总共带宽。MBR和GBR是EPS承载的最大和保证带宽，AMBR是多个EPS承载的最大总共带宽。

GBR EPS承载分配GBR和MBR，意味着为这个承载分配专用的网络资源。而，non-GBR EPS承载分配一个AMBR，意味着对这个承载没有分配专用的网络资源，但是分配了一个和其他non-GBR承载共享的最大带宽。由两种类型的AMBR，APN-AMBR（是在PDN中由所有的non-GBR承载共享的最大带宽）和UE-AMBR（在ＵＥ中共同分享的最大带宽）。一个UE可以连接到不止一个PDN，也就是说，所有PDN的总APN-AMBR不能超过UE-AMBR。这两种类型的QoS参数如下：

 - GBR承载QoS参数：QCI、ARP、GBR（UL/DL）、MBR（UL/DL）
 - non-GBR承载QoS参数：QCI、ARP、APN-AMBR（UL/DL）、UE-AMBR（UL/DL）

**SDF和EPS承载QoS参数**
图2显示了应用于SDF和EPS承载的QoS参数。

![这里写图片描述](http://img.blog.csdn.net/20150913094816557)

在图2中，UE连接两个PDN。UE有两个IP地址：IP地址1由PGW1分配用于PDN1，IP地址2由PGW2分配用于PDN2。每一个PDN有一个默认承载和两个专用承载。在PGW使用SDF templates把用户IP数据流分成了不同的SDF。从PDN1和PDN2由两组SDF（1-5）。对于这些SDF，根据在PGW设置的QoS规则来分配网络资源和数据包转发策略。并基于他们特定的QCI和ARP映射到EPS承载上。图中，对于PDN1，SDF1/2映射到默认承载，SDF3/4映射到non-GBR专用承载，SDF5映射到GBR专用承载，所有的都传送到UE。这样的从SDF到EPS承载的映射是由TFT决定的。所有用户流量在通过EPS承载传输时都准守EPS承载QoS。

所有和PDN关联的non-GBR承载都是由他们共享的最大APN-AMBR控制，而和UE关联的non-GBR承载都是由他们共享的最大UE-AMBR控制。

表2 SDF和EPS承载的QoS参数

项|描述
---|---
QCI|指示不同的QoS性能特征；标准QoS特征值用QCI=1-9定义；QCI代表了QoS的下列特征：资源类型（GBR or non-GBR），优先级（1-9），数据包延迟预算（50-300ms），数据包错误丢失率（10-2~10-6）；在网络节点（eNB，SGW，PGW）上控制数据包转发（调度权重，准入阈值，队列管理阈值，链路层协议配置等等）；在运营商网络节点（例如eNB）预先配置。
ARP|ARP参数：优先等级，抢占能力，被抢占能力；优先级（1-15）：定义资源请求的相对重要性，1是最高。抢占能力（yes or no）：定义SDF是否占用已经分配给一个优先级较低的SDF/承载的资源。被抢占能力（yes or no）：定义SDF是否能失去已经分配给它了，但是为了建立更高优先级的SDF/承载的资源。用于控制呼叫准入：指示一个优先级值用于决定在网络资源有限的条件下一个新的SDF/EPS承载需要激活或者修改的情况下是否拒绝激活一个新的SDF/EPS承载或移除存在的SDF/EPS承载。仅当在决定是否激活一个新SDF/EPS承载时需要考虑。一旦成功的建立，ARP对数据包转发处理没有影响。
GBR (UL/DL)|应用于GBR SDF/承载。指示对GBR SDF/承载能保证的最小的带宽
MBR（UL/DL）|应用于GBR/non-GBR SDF和承载。指示对SDF/承载允许的最大带宽；任何超过指定MBR的数据流量都通过数据策略丢弃。
APN-AMBR(UL/DL)|每一个PDN定义一个。指示对所有和PDN相关的non-GBR承载所允许的最大带宽；只适用于non-GBR聚合带宽
UE-AMBR(UL/DL)|每一个UE定义一个。指示对所有和UE相关的non-GBR承载允许的最大带宽；只适用于non-GBR承载的聚合带宽；这个是由HSS提供的签约信息（UE-AMBR-HSS）。但是仍然可以由MME修改，只要不超过所有APN的APN-AMBR的许可范围即可（在UE-AMBR-HSS范围之内）。
GBR SDF/承载|给这种类型的SDF/承载分配专用的网络资源来达到保证的GBR。当没有流量的时候，网络总是为GBR SDF/承载保持GBR指定的带宽。
non-GBR SDF/承载|给这种类型的SDF/承载不分配专用的网络资源。依据网络的拥挤程度以最大能力来传输流量。

# IV. QoS Provisioning and Enforcement

4.1节将讨论QoS分配：QoS参数是在哪个实体中设置的。4.2节讨论QoS实施：决定哪个实体设置和应用哪个用户流量参数。

## 4.1 QoS分配

图3展示了QoS参数是在哪个实体中设置的；
![这里写图片描述](http://img.blog.csdn.net/20150913113710510)

**SDF QoS分配**
SDF所有的QoS参数是由PCRF提供的（Policy and Charging Rules Function）。

**EPS承载QoS分配**
应用于默认承载的QoS参数是由网络运营商作为签约信息提供给HSS的。当默认承载激活的时候，MME从HSS中下载关于这个承载的QoS配置信息，并发送给相应的EPS实体。一旦EPS回话建立，QoS规则经过PDRF授权时，由HSS提供给默认承载的QoS参数可以修改。由HSS提供的UE-AMBR由eNB控制，但可以由MME修改。在修改的情况下，MME可以使用所有正在激活的PDN的聚合APN-AMBR取代UE-AMBR，只要这个值没有超过由HSS提供的UE-AMBR-HSS值即可。

应用于专用承载的QoS参数是由PCRF提供的。当承载激活时，PCRF基于从SPR接收到的签约信息来决定承载的QoS参数。

## 4.2 QoS实施

在QoS实施过程中，执行用户流量的检测，然后对每一个检测到的SDF和EPS承载执行相应的QoS规则。
图4显示了SDF和EPS承载是在哪个实体中设置和实施的。EPS承载QoS参数实施于SGW和实施于PGW是一样的，除了APN-AMBR之外。但是为了绘图方便，在这个图中只显示了QCI。

![这里写图片描述](http://img.blog.csdn.net/20150913123733111)

**SDF QoS实施**
SDF QoS参数是存在于PGW中。表4显示了SDF QoS在哪个实体中实施。到达PGW的IP数据流使用SDF templates过滤为不同的SDF，接着这些SDF由SDF QoS参数控制安装在PGW中。

![这里写图片描述](http://img.blog.csdn.net/20150913124053150)

**EPS承载QoS实施**
EPS承载的QoS参数实施在UE和PGW之间传输用户流量的EPS实体上。表5描述了每一个QoS参数在哪个EPS实体中实施。APN-AMBR被承载的两个端点（UE和PGW）应用在PDN上激活的所有non-GBR EPS承载。APN-AMBR在UE中仅仅用于上行流量，但是在PGW中应用于上行和下行流量。而UE-AMBR由所有PDN发端的eNB来应用在UE上激活的所有non-GBR EPS承载。也就是说APN-AMBR只应用于和它关联的APN所表示的PDN，而UE-AMBR应用于UE，所有和UE关联的PDN。
MBR只用于GBR承载，在UE和eNB中是用于上行流量，而在SGW和PGW中只应用于下行流量。GBR只适用于GBR EPS承载，对上行和下行除了UE之外的所有实体都适用。

![这里写图片描述](http://img.blog.csdn.net/20150913132200815)

# V. An Example for SDF and EPS Bearer QoS

在第五章，举例说明LTE QoS是如何基于SDF和EPS承载QoS提供的。通过这个实例，可以学习SDF和EPS承载QoS怎么工作和他们是在哪个实体中提供的。使用的场景如下：

 - UE连接到一个PDN（因特网）
 - UE通过三个承载和因特网通信（一个默认承载，一个GBR承载和一个non-GBR承载）
 - 他们承载的ID（EPS承载ID EBI）是5,8,10.

## 5.1 下行的QoS操作

图5显示了下行QoS操作的实例。在每一个实体的操作详细在下面描述。这里，可适用的流量控制包括流量监管和流量成型。图5和图6是应用流量监管的实例。

![这里写图片描述](http://img.blog.csdn.net/20150913133018940)

 ❶ [P-GW] 下行IP数据流到达
IP数据流到达PGW，数据流1-5分别是语音数据（RTP），视频数据流，语音信令（SIP），双向的游戏，尽力而为的因特网流量。

❷ [P-GW] IP数据包过滤 (SDF Templates)
一旦PGW接收到数据包，IP数据通过IP数据包过滤器 (SDF templates)被过滤成不同的SDF。在这里，五元组（源IP地址，目的IP地址，源端口，目的端口，协议ID）用来充当过滤规则。IP数据流1被分类为GBR SDF1，数据流2被分类为GBR SDF2，IP数据流3/4被分类为non-GBR SDF3，IP数据流5被分类为non-GBR SDF4.

❸ [P-GW] SDF QoS实施: MBR速率策略
MBR速率策略对每一个SDF执行，任何超过指定下行MBR的流量都将被丢弃。

❹ [P-GW] SDF – EPS 承载映射: IP 数据包过滤 (Traffic Flow Templates; TFT)
SDF使用IP数据包过滤TFT被过滤成不同的EPS承载。SDF1和SDF2映射到GBR专用承载EBI=10。SDF3被映射被non-GBR专用承载EBI=8，SDF4被映射为non-GBR默认承载EBI=5。

❺ [P-GW] EPS 承载QoS 实施: MBR/APN-AMBR 速率策略
EPS承载QoS适用于每一个承载。对于GBR承载，MBR速率策略是使用下行MBR来执行，任何IP数据包超过指定下行MBR的都被丢弃。对于non-MBR承载，APN-AMBR速率策略被执行。也就是说，对于所有发往EBI=5/8的IP数据流都是适用速率策略，任何IP数据包超过指定的下行APN-AMBR的都被丢弃。

❻ [eNB] EPS 承载QoS 实施: UE-AMBR 调度
eNB对non-AMBR执行UE-AMBR速率策略，并且在无线链路上调度。也就是说对于所有发往EBI=5/8的IP数据流都执行下行UE-AMBR。在图5中，因为只有一个PDN，下行UE-AMBR和下行APN-AMBR是相等的。

## 5.2 上行QoS操作

图6显示了上行LTE QoS的操作。不像下行，MBR和APN-AMBR的控制在上行方向在ＵＥ和PGW中都执行。

![这里写图片描述](http://img.blog.csdn.net/20150913135046775)

❶ [UE] 上行IP 数据流到达
从用户应用层的IP数据流到达ＵＥ，在这里，应用层和下行的是一样的。

❷ [UE] IP数据包过滤 (TFT)
上行IP数据流被IP数据包过滤TFT过滤为EPS承载。在这里，IP和TCP/UDP协议头中的五元组用于过滤规则。IP数据流1/2映射到GBR专用承载EBI=10，IP数据流3/4被映射到non-GBR专用承载EBI=8，IP数据流5被映射为默认承载EBI=5.

❸ [UE] EPS 承载QoS 实施: MBR/APN-AMBR速率策略
EPS承载QoS应用于每一个EPS承载。对于映射到GBR专用承载EBI=10的IP数据流，使用上行的MBR来执行速率策略，对于non-GBR专用承载EBI=5/8的所有IP数据流使用上行APN-AMBR来执行速率策略。

❹ [eNB] EPS 承载QoS 实施: MBR/UE-AMBR速率策略
eNB对GBR承载EBI=10使用上行MBR执行速率策略和调度，对于non-GBR承载EBI=5/8使用上行UE-AMBR来执行速率策略和调度。认为只有一个PDN，上行UE-AMBR和上行APN-AMBR是相等的。

❺ [P-GW] 承载流量到达
承载数据流通过SGW到达PGW

❻ [P-GW] EPS 承载QoS 实施: APN-AMBR 速率策略
对所有的从non-GBR承载接收到的IP数据流执行APN-AMBR。任何超过上行APN-AMBR的数据包都被丢弃。

❼ [P-GW] IP数据包过滤 (SDF Templates)
EPS bearers are filtered through IP packet filters (SDF templates) into different SDFs. IP flows 1 and 2 from
the GBR dedicated bearer (EBI=10) are mapped to SDFs 1 and 2, IP flows 3 and 4 from non-GBR dedicated
bearer (EBI=8) are mapped to SDFs 3 and 4, and finally IP flows 5 from the default bearer (EBI=5) is
mapped to SDF 5.
EPS承载被IP数据包过滤SDF templates过滤成不同的SDF。GBR专用承载EBI=10的IP数据流1/2被映射为SDF1/2。non-GBR专用承载EBI=8的IP数据流3/4被映射为SDF3/4。默认承载的IP数据流5被映射为SDF5.

❽ [P-GW] SDF QoS 实施: MBR 速率策略
对每一个SDF执行MBR速率策略，任何超过指定上行MBR的IP数据包都会被丢弃。

# VI. Closing

我们已经学习了在服务层次和承载层次上的LTE QoS机制。我们也学习了用户IP数据流在服务层次上被分为不同的SDF，接着用户数据流在承载层次上被分类为不同的EPS承载。我们也讨论了SDF和EPS的他们互相映射的关系。
在eNB的MAC层给ＵＥ分配无线资源并基于EPS承载QoS参数来执行数据包调度。eNB数据包调度不在本文档的范围内。决定和鉴权QoS参数的具体过程将会在接下来的文档中描述。

# References
[1] Netmanias Technical Document, “LTE Identification III: EPS Session/Bearer Identifiers”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5907
[2] 3GPP TS23.203, “Policy and charging control architecture”.
[3] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.

