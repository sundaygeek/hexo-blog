---
title: '4-LTE Identification III: EPS Session/Bearer Identifiers'
date: 2016-04-03 21:40:55
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
    
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=5907

# I. Introduction
在LTE Identification I II中我们学习了UE ID，ME ID，NE ID和Location ID。这篇文档将学习和用户数据传输有关的EPS会话/承载ID。端到端的会话包含应用层实体不在本篇文档讨论范围，仅仅包含提供用户用于PDN连接的EPS会话。在表1表示这篇文档讨论的ID。
![这里写图片描述](http://img.blog.csdn.net/20150910140828600)
我们在LTE identification I II中学习了不同的UE，ME，NE，有多个EPS会话ID和承载ID在一个UE中使用。这意味着，在一个UE中可以同时建立不止一个EPS会话和承载，所以需要ID来标识他们。

# II. EPS Session and EPS Bearer: Overview
在我们讨论和EPS会话和承载相关的ID之前，我们需要了解一下EPS会话和承载是什么，并且描述一下这些ID之间的关系。
图1显示了一个用户的EPS会话和承载，并在下面标识了ID。
![这里写图片描述](http://img.blog.csdn.net/20150910141940302)

## 2.1 EPS会话
UE和PDN之间的IP连接叫PDN连接或EPS会话。每一个PDN连接是由UE的IP地址和PDN ID（也叫APN）描述的。它有不止一个EPS承载来传输用户数据（IP数据包），并应用从PCRF到EPS承载获得的服务质量（QoS）策略。最小最基本的承载是EPS会话的一个默认EPS承载。

一旦EPS会话建立意味着1）已经选择一个用户使用服务的PDN（通过用户的输入或者基于HSS提供的签约信息），2）已经为用户分配了用于PDN内的IP地址，3）已经选择了用于用户IP数据包的策略规则（QoS和计费策略），4）在LTE网络上传送IP数据包的默认EPS承载已经建立了。通过已经建立的这个EPS承载，IP数据报可以通过运营商提供的策略在用户和PDN之间相关交换。
会话的管理和操作，包括PCRF会在其他文档讨论，PDN ID（APN）将会作为和EPS会话相关的一个ID在本文档中讨论。

## 2.2 EPS承载
EPS承载负责传送和处理标记了UE IP地址的IP数据流，并在UE和PDN之间传输（UE – P-GW – PDN）。另一个方面，一个EPS承载就是LTE网络上传输IP数据包的管道，例如，在UE和PGW之间(UE – eNB – SGW - P-GW)。一个UE可以同时又多个EPS承载。不同EPS承载使用MME分配的EPS bearer ID来标识。
如图1所示，一个EPS承载是由以下三个承载串接起来的（DRB，S1承载和S5承载）:

 - [UE] - [eNB]: 无线数据承载 (DRB)，EPS承载在LTE-Uu接口上建立。用户数据（IP数据包）通过DRB来传输。不同的DRB使用eNB分配的不同的DRB ID来标识。
 - [eNB] - [S-GW]: S1承载，EPS承载在S1-U接口上建立。用户数据通过GTP隧道传输。不同的S1承载使用由GTP隧道端点（(eNB and S-GW)）分配的TEID来标识。
 - [S-GW] - [P-GW]: S5承载，EPS承载在S5接口上建立。用户数据通过GTP隧道传输。不同的S5承载使用由GTP隧道端点（(S-GW and P-GW) ）分配的TEID来标识。

E-RAB是有UE和SGW两个端点的承载，包含DRB和S1承载。E-RAB是由DRB和S1承载串接，并在从UE到SGW连接(UE –eNB – S-GW)。不同的E-RAB使用由MME分配的E-RAB ID来标识。DRB ID和E-RAB ID是和EPS承载ID一对一映射的。

## 2.3 EPS承载的类型

之前我们描述了EPS相关的承载ID，现在我们看看EPS承载的不同类型和他们是怎么工作的。图2描述了EPS承载的不同类型：默认承载和专用承载。每一个PDN必须有一个默认的EPS承载，但可以没有或者有多个专用ESP承载。
![这里写图片描述](http://img.blog.csdn.net/20150910152738149)

LTE网络是全IP网络，提供给用户永久在线的IP连接。这就意味着，一旦UE使用初始附着到网络分配的IP地址连接到PDN，在LTE网络上在默认EPS承载建立之后，IP连接将一直保持连接，直到UE从LTE网络去附着（比如，PDN连接终止了）。即使用户没有数据要发送，这个默认的EPS承载也是一直保持激活，并准备着可能到达的用户数据。

如果默认的EPS承载不能足够满足QoS，则需要建立额外的EPS承载。建立的额外EPS承载叫做专用EPS承载，并且根据用户或者网络的需要可以建立多个专用承载。当没有用户数据，这些专用EPS承载可以被移除，但默认的EPS承载不会被移除，一直保持用户和网络之间的连接，直到用户从网络去附着。专用EPS承载是和默认EPS承载联接在一起的。这个联接的承载是用LBI（Linked EPS Bearer Identity）标识，指示他们都联接到同一个默认EPS承载。

UE发送或者UE接收的IP数据都是在LTE网络上根据QoS等级通过适当的EPS承载来传输。上行IP数据从UE一直映射到EPS承载，而下行IP数据是从PGW一直映射到EPS承载。

根据2.2和2.3节讨论的，和EPS承载相关的ID有EPS Bearer ID，E-RAB ID，DRB，TEID，LBI。第三章将详细介绍这些EPS会话和承载ID。

# III. Identifiers for EPS Session/Bearer (Session/Bearer IDs)

## 3.1 识别PDN的ID：PDN ID(APN)
PDN使用PDN ID（APN）来标识。一个APN关联一个用户想连接PDN的接入点。在图3是APN和格式。APN是由网络ID和运营商ID组成。网络ID用来识别PDN，像因特网、企业VPN，或者用来识别PDN提供的服务，像IMS。

APN是在用户签约时由HSS作为签约信息提供的。一旦UE初始附着，一个默认的APN从HSS下载到MME。MME首先基于APN选择一个PDN连接UE，接着通过UE连接到的PDN选择PGW。在图3中，MME基于APN1选择PDN1，然后选择PGW 1建立PDN1连接。
![这里写图片描述](http://img.blog.csdn.net/20150910165208648)

## 3.2 在EPS网络标识用户数据传输的ID：EPS承载ID
EPS承载是UE和PGW之间虚拟连接集合，用来LTE网络上传输用户数据。EPS承载中不同的承载是用4bit的EPS承载ID标识的。表2是EPS承载ID值。一个UE可以最多11个EPS承载，他们的ID值从5-15。
![这里写图片描述](http://img.blog.csdn.net/20150910165645110)

图4是EPS承载相关的ID和他们是谁分配的。EPS承载ID，不论默认的还是专用的，都是由MME分配的。当UE初始附着到LTE网络，MME从HSS中获得建立默认EPS承载需要的QoS配置文件，并基于接收到的QoS建立起承载。当MME分配EPS承载ID时，发起默认EPS承载建立的过程。
![这里写图片描述](http://img.blog.csdn.net/20150910170041493)
EPS承载的两个端点是UE和PGW。他们都执行数据流过滤（UE过滤上行用户数据流，PGW过滤下行数据流）来通过数据流要发送的哪一个承载来决定。图2是包含五个IP数据流（1-5）的下行用户传输。当IP数据流从PDN到达PGW时，PGW实行数据流过滤来决定IP数据流传输使用哪一个承载，并使用相应的承载来传输。下行EPS承载数据是通过S5承载，S1承载和DRB最终到达UE，并把数据流以IP流的方式传输到上层。为了让这个过程进行，每一个实体都必须对每一个承载映射承载ID，如表3。图5是映射的过程。
![这里写图片描述](http://img.blog.csdn.net/20150911082359452)
![这里写图片描述](http://img.blog.csdn.net/20150911082407699)

## 3.3 在UE和EPC之间来识别EPS的ID：E-RAB ID

如图1/4/5所示，E-RAB是UE和SGW之间的一个EPS承载集合，用4bit的E-RAB ID来标识。E-RAB ID是由MME分配，通常和EPS承载ID有相同的值（根据EPS承载的建立），并和EPS承载ID一对一的对应。在EPS承载建立过程中，当MME请求eNB建立E-RAB时，eNB和UE创建DRB，和SGW创建S1承载。

默认EPS承载使UE一直和网络保持连接。当没有用户数据，UE保持在空闲状态，E-RAB是去激活的，只有S5承载保持存在。但是，只要用户数据到达，E-RAB重建，然后允许传送数据流从UE到PGW。

## 3.4 在无线链路上标识EPS承载的ID：DRB ID
如图1/4/5所示，DRB是在UE和eNB之间在无线链路上的一个集合，用4bit的DRB ID来标识。一旦建立EPS承载，DRB ID由eNB分配，并且和EPS承载ID一对一对应。在EPS承载建立过程中，当MME请求eNB建立E-RAB时，eNB分配DRB ID创建DRB来和UE通信，并基于需求的QoS选择合适的逻辑信道配置参数。

## 3.5 识别GTP隧道端点的ID：TEID
S1承载和S5承载，都是EPS承载的一个部分，分别在eNB和SGW之间，在SGW和PGW之间以GTP隧道的方式建立。GTP隧道是以上行和下行两个端点的TEID标识的，TEID是32bit。图4显示S1和S5 GTP隧道上TEID的分配。当EPS承载建立时，对于S5承载，SGW分配DL S5 TEID，PGW分配UL S5 TEID。对于S1承载，SGW分配UL S1 TEID，eNB分配DL S1 TEID。

## 3.6 连接默认EPS承载和专用EPS承载的ID：LBI
如图2所示，一个EPS会话有不止一个EPS承载。默认EPS承载在当EPS会建立时建立，在EPS会话结束时结束。另一方面，专用EPS承载可以在EPS会话建立后随时创建和删除。因为默认承载和专用承载都属于同一个用户的同一个PDN，所以需要一个ID来标识用于同一个PDN的两个不同的承载。这个引入的ID是LBI，默认EPS承载ID用作LBI。

当默认EPS承载创建时，MME分配一个承载ID，这个ID也作为一个LBI指定。接着，当专用EPS承载创建时，MME分配LBI（和承载ID一起）来标识默认承载。

# IV. Summary
我们已经表1中分类了EPS会话和承载相关的ID。表4分类和列出他们使用的范围等。表5总结所有的LTE ID。
![这里写图片描述](http://img.blog.csdn.net/20150911144201560)
![这里写图片描述](http://img.blog.csdn.net/20150911144218481)
![这里写图片描述](http://img.blog.csdn.net/20150911144228361)
![这里写图片描述](http://img.blog.csdn.net/20150911144244656)
![这里写图片描述](http://img.blog.csdn.net/20150911144253609)

# V. Closing
通过LTE identification文档，我们全部介绍了表1中的LTE ID，并且简要的介绍了他们的功能和目的。基于我们经学到的LTE网络架构和LTE identification，我们可以继续讨论高主要的议题。

# References
[1] Netmanias Technical Document, “LTE Identification I: UE and ME Identifiers”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5905
[2] Netmanias Technical Document, “LTE Identification II: NE and Location Identifiers”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5906
[3] Netmanias Technology Document, “LTE Network Architecture: Basic”, July 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5904
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.
