---
title: '1-LTE Network Architecture: Basic'
date: 2016-04-03 20:57:47
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE

---


原文地址：http://www.netmanias.com/en/?m=view&id=techdocs&no=5904

# I. Introduction
这篇文章主要介绍LTE网络结构，是LTE的第一篇技术文章。LTE网络也叫EPS，是一个端到端的全IP网络；EPS分为两个部分，LTE部分负责处理和接入网络E-UTRAN相关的技术，EPC部分负责处理和核心网相关的技术。一个端到端的全IP网络一位置所有的数据流，从UE到连接服务实体的PDN之间的所有通路，在EPS内部，所有的传输都是基于IP协议的。

为了通过LTE网络提供给用户LTE服务，引入了一个端到端LTE网络的参考模型（NRM），这个模型除了基本的EPS域之外，还包含三个额外的域（用户的BSS/OSS域，提供服务的应用域，发送IP包的IP传输网络域）。这篇文章的范围集中在基本的EPS域。其他的EPS域特性，例如，LTE和其他3gpp/3gpp2的互操作性和漫游，这些特性是在本文档范围之外，后续会在其他文档中涉及。

这个文档剩余部分的组织如下，在第二章，定义LTE网络参考模型，解释EPS实体和接口。第三章描述协议栈架构。第四章描述整个LTE网络的数据流。


---


# II. LTE Network Reference Model
图1是LT网络的参考模型，包含LTE实体（UE和eNB）和EPC实体（SGW/PGW/MME/HSS/PCRF/SPR/PCSOFCS）。PDN是和UE进行通信的运营商内部或者外面IP域，它提供给UE服务，比如，因特网，IMS。表1和表2是LTE和EPC实体的功能。表3列出了LTE网络参考模型的参考点，并对EPS实体之间的接口进行了描述。

![这里写图片描述](http://img.blog.csdn.net/20150909120950694)

表格1：LTE实体

实体|描述
------|-----
UE|UE通过LTE-Uu接口连接eNB
eNB|eNB提供用户无线接口，并负责无线资源管理功能，例如，动态资源分配（调度器），eNB测量配置和规定，无线准入控制，连接移动性控制，无线承载控制，小区间干扰协调。

表格2：EPC实体

实体|描述
------|-----
MME|MME是E-UTRAN的主控制实体。它和HSS通信来用户鉴权和用户配置文件下载，并使用NAS信令提供给UE EPS移动性管理（EMM）和EPS会话管理（ESM）功能。MME支持的主要功能包括：NAS信令（EMM/ESM/NAS安全）；通过S6a接口和HSS通信负责用户鉴权和漫游；移动性管理（寻呼/TAI管理/切换管理）；EPS承载管理。
SGW|SGW终止E-UTRAN方向的接口。它为eNB间和3gpp间切换的数据传输提供了本地移动锚点。
PGW|PGW通过从PDN的地址空间分配IP地址提供给UE接入PDN的能力。PGW作为在3gpp和non-3gpp之间切换的移动锚点。PGW也执行策略实施，数据包过滤和由PCRF提供的基于PCC规则的计费。PGW主要的功能包括：IP路由和转发，数据包过滤，UE IP地址分配，在3gpp和non-3gpp之间切换的移动锚点，PCEF功能，计费。
HSS|HSS是存储用户配置的中心数据库。它提供给MME用户鉴权信息和用户配置文件。
PCRF|PCRF是策略和计费控制实体。它为服务数据流（SDF）提供策略决策，并且提供给PCEF（PCEF在PGW内）策略和计费控制（PCC）规则（包括QoS和计费规则）
SPR|SPR提供给PCRF用户信息（每个用户的接入配置文件）。PCRF接收到这个信息后，将执行基于用户的策略并创建策略和计费控制（PCC）规则。
OCS|OCS提供实时的信用控制和基于体积/时间/事件的计费功能。
OFCS|OFCS提供基于计费数据记录（CDR）的计费信息。

CDR：charging data record
OCS：online charging system
OFCS：offline charging system
OSS：operations support system
PCC：policy and charging control
PCEF：policy and charging enforcement function
PCRF：policy and charging rules function
SDF：service data flow
SPR：subscriber profile repository

表3：LTE接口

参考点|协议|描述
---|-----
LTE-Uu|E-UTRA（控制面和用户面）|UE和E-UTRAN（eNB）之间控制和用户面的接口。在LTE-Uu接口上，信令连接是用SRB表示的RRC连接，用户面连接是用DRB表示的逻辑信道。
X2|X2-AP（控制面）GTP-U（用户面）|两个eNB之间的控制和用户面的接口。它用于在X2切换或自组网（SON）相关的功能。控制面是使用的X2-AP协议，用户面为数据转发的每一个承载提供GTP-U隧道。
S1-U|GTP-U|在eNB和SGW之间用户面的接口。它为每一个承载提供GTP隧道。
S1-MME|S1-AP|eNB和MME之间控制面的接口。
S11|GTP-C|MME和SGW之间控制面的接口。它为每一个承载提供GTP隧道。
S5|GTP-C（控制面）GTP-U（用户面）|SGW和PGW之间的控制和用户面接口。S5接口为用户面每一个承载提供GTP隧道，为控制面的每一个用户提供GTP隧道管理（创建/修改/删除）。对PLMN间，使用S8接口替代。S8接口在本文档范围之外，将在其他文档中描述。
S6a|Diameter|HSS和MME之间控制面的接口。它用于交换用户订阅和鉴权信息
Sp|Diameter|SPR和PCRF之间控制面的接口。
Gx|Diameter|PCRF和PGW之间控制面的接口。它从PCRF到PGW传输策略控制和计费规则来支持QoS策略和计费控制。
Gy|Diameter|OCS和PGW之间控制面的接口。
Gz|GTP'|OFCS和PGW之间控制面的接口。
SGi|IP|PGW和PDN之间控制和用户面的接口。在用户面使用基于IETF的IP数据包转发协议，在控制面使用DHCP和RADIUS/Diameter协议。


---


# III. LTE Protocol Stacks

这一章主要介绍LTE用户面和控制面的协议栈。
## 3.1 用户面协议栈
图2是LTE网络参考模型的用户面协议栈。接着介绍协议栈主要层的功能描述。
![这里写图片描述](http://img.blog.csdn.net/20150909135820709)
### 1）LTE-Uu接口

 - PDCP：PDCP支持在无线链路上IP数据包的有效性传输。它可以执行协议头压缩，接入层安全（加密和完整性保护），数据包重排序和重传。
 - RLC：在发送端，RLC构建RLC PDU，并提供给MAC层。RLC协议负责PDCP PDU的分片和串接。在接收端，RLC协议通过对RLC PDU的重组来重新构建PDCP PDU。RLC协议有三种操作模式（透明模式，确认模式，非确认模式），每一种模式提供不同的可靠性级别。RLC也数据包（RLC PDU）的重排序和重传。
 - MAC：MAC层在RLC和PHY层之间。它通过逻辑信道和RLC层连接，通过传输信道和PHY层连接。因此，MAC层协议在逻辑信道和传输信道之间支持复用和解复用。高层使用不同的逻辑信道来满足不同的QoS要求。MAC层通过对逻辑信道数据的调度和优先级来支持不同的QoS。eNB调度器可以使无线资源动态分配给UE，并通过执行QoS控制来确保每一个承载都分配了协商的QoS。


### 2) S1-U/S5/X2 接口

 - GTP-U：GTP-U协议通过在S1-U/S5/X2接口上来转发IP数据包。当在LTE切换过程中已经建立了用于数据转发的GTP隧道，则结束标记数据包应该在GTP隧道上作为最后一个数据包传输。

## 3.2 控制面协议栈
图3是LTE网络参考模型的控制面协议栈。接着介绍协议栈主要层的功能描述。
![这里写图片描述](http://img.blog.csdn.net/20150909144404890)
### 1) LTE-Uu接口
 - NAS：NAS协议负责移动性管理和承载管理功能。
 - RRC：RRC协议对NAS信令的传输提供支持。它也负责无线资源的有效性管理功能。主要的功能包括：系统消息的广播，RRC连接的建立/重配/重建/释放，无线承载的建立/修改/释放。

### 2) X2接口

 - X2AP：X2AP协议支持在E-UTRAN内UE的移动性和自组网功能。为了支持UE移动性，X2AP协议提供了例如用户数据转发，序列号状态的传输，UE上下文的释放的功能。对自组网功能，eNB之间交换资源状态信息，负载信息，eNB配置更新信息，使用X2AP协议来相互协调来满足移动性参数。

### 3) S1-MME接口

 - S1AP：S1AP协议支持例如S1接口管理，E-RAB管理，NAS信令传输和UE上下文管理。它传输初始UE上下文给eNB来建立E-RAB，并且管理后续UE上下文的修改和释放。

### 4) S11/S5/S10接口

 - GTP-C：GTP-C协议支持GTP隧道的建立/修改/终止控制信息的交换。它在LTE切换时创建数据转发的隧道。

### 5) S6a接口

 - Diameter：Diameter协议支持在HSS和MME之间用户订阅和鉴权信息的交换。

### 6) Gx接口

 - Diameter：Diameter协议支持把PCC规则从PCRF发送到PCEF（PGW）。

### 7) Gy接口

 - Diameter：Diameter协议支持在PGW和OCS之间实时信用控制信息的交换。

### 8) Gz接口

 - GTP’：GTP’协议支持CDR从PGW发送到OFCS。

---

----------
# IV. Traffic Flow on the LTE Network

图4是在LTE网络参考架构中，用户面数据流接入因特网的过程。图4a是数据流从UE到因特网，图4b是数据流从因特网到UE。IP数据包是在S1-U和S5接口上通过GTP隧道来转发的。这些隧道是在用户附着在LTE网络时对每一个EPS承载建立的。

在S1-U和S5的每一个接口上有多个EPS承载建立。因此，为了识别这些承载，每一个GTP隧道的端点都分配了一个TEID（隧道端点标识），（通常识别一个GTP隧道，采用TEID，IP地址，UDP端口号。但是，在这里，为了描述的方便，仅仅使用了TEID）。GTP隧道的接收端本地分配的TEID，发送端必须使用。TEID的交换是在隧道端点使用控制面协议交换的。

![这里写图片描述](http://img.blog.csdn.net/20150909144940673)

当在S1-U接口上建立了GTP隧道时，SGW分配一个TEID用于上行传输，eNB分配一个TEID用于下行传输。S1 GTP隧道TEID是在eNB和SGW之间使用S1AP和GTP-C消息来交换的。同样的，当在S5接口上建立了GTP隧道时，PGW分配一个TEID用于上行传输，SGW分配一个TEID用于下行传输。S5 GTP隧道TEID是在SGW和PGW之间使用GTP-C消息来交换的。

当用户的数据包在S1-U和S5接口上通过GTP隧道传输时，eNB/SGW/PGW通过封装接收对端GTP实体分配的TEID来转发用户IP数据包。在上行方向，SGW在S1 GTP（UL S1-TEID）隧道和S5 GTP（UL S5-TEID）隧道间建立一对一的映射，来终止S1 GTP隧道，并转发用户数据包到S5 GTP隧道。同样的，在下行方向，SGW在S5 GTP（DL S5-TEID）隧道和S1 GTP（DL S1-TEID）隧道间建立一对一的映射，来终止S5 GTP隧道，并转发用户数据包到S1 GTP隧道。在图4中，通过哪一个EPS实体转发因特网数据流的过程如下所示：

### a）上行方向数据流，从UE到因特网

 1. UE通过LTE-Uu接口传输用户IP数据包到eNB。
 2. eNB使用S1 GTP隧道协议头封装用户IP数据包，并转发封装好的IP数据包给SGW。在这里，eNB使用TEID/目的IP地址/源IP地址作为S1 GTP隧道协议头。
 3. 在接收到封装好的IP数据包后，SGW剥离掉S1 GTP隧道协议头，并且使用S5 GTP隧道协议头封装用户IP数据包，并转发封装后的IP数据包。在这里，SGW使用TEID/目的IP地址/源IP地址作为S5 GTP隧道协议头。
 4. 在接收到封装好的IP数据包之后，PGW剥离掉S5 GTP隧道协议头得到用户IP数据包，并通过IP路由转发到因特网。

### b）下行方向数据流，从因特网到UE

 1. PGW从因特网接收到一个目的为UE的IP数据包。
 2. PGW使用S5 GTP隧道协议头封装用户IP数据包，并转发封装好的IP数据包给SGW。在这里，PGW使用TEID/目的IP地址/源IP地址作为S5 GTP隧道协议头。
 3. 在接收到封装好的IP数据包后，SGW剥离掉S5 GTP隧道协议头，并且使用S1 GTP隧道协议头封装用户IP数据包，并转发封装后的IP数据包。在这里，SGW使用TEID/目的IP地址/源IP地址作为S1 GTP隧道协议头。
 4. 在接收到封装好的IP数据包之后，eNB剥离掉S1 GTP隧道协议头得到用户IP数据包，并在无线链路上通过DRB传输给UE。

# V. Closing
LTE网络架构作为第一个LTE系列技术文档。这个文档描述的LTE网路架构只适用于单一运营商的LTE only网络，并描述EPS系统最基本的部分。为了更理解以后的LTE文档，对实体/接口的基本理解是必须的。下一个文档，是另一个基本的文档，讨论应用于LTE网络参考模型的LTE标识。这些基本文档对很好的理解后续的文档有很大帮助，会接着讨论LTE网络更高级的功能，包括LTE互操作和漫游。

# References
[1] 3GPP TS 36.300, “Evolved Universal Terrestrial Radio Access (E-UTRA) and Evolved Universal
Terrestrial Radio Access Network (E-UTRAN); Overall description; Stage 2”.
[2] 3GPP TS 23.401, “General Packet Radio Service (GPRS) enhancements for Evolved Universal
Terrestrial Radio Access Network (E-UTRAN) access”.
[3] Magnus Olsson, et. al., SAE and the Evolved Packet Core – Driving the Mobile Broadband Revolution,
AP, 2009.
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.
