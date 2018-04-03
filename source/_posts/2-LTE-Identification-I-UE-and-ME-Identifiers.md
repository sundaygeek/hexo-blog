---
title: '2-LTE Identification I: UE and ME Identifiers'
date: 2018-04-03 21:16:55
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---

原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=5905

# I. Introduction
在LTE网络中，根据和其他ID的关系使用不同的ID来标识每一个实体。就像在人际网络中使用不同的名字和头衔来标识一个人。理解这些ID和EPS实体是对理解LTE技术很必要。

我们之前讨论了第一个问题--LTE网络架构。现在我们讨论LTE标识，这是我们的第二个问题，将会有三个相关的文档来讨论。这个文档是第一个，聚焦在用户设备（UE） ID。第二个和第三个文档将f分别涉及到网络设备（NE）ID和EPS会话/承载标识。

这个文档组织如下。第二章，对LTE ID分为几个组。第三章和第四章，将分别描述UE ID和ME ID。第五章，总结UE和ME ID的总体特征。


----------


# II. Classification of LTE Identification

图1表示，在LTE网络参考模型一些实体和接口使用和定义的ID。这些LTE ID将按照他们的生成时间/属性类型（固定的或暂时的）/其唯一标识的范围。

创建时间：LTE ID的创建时间有以下几种

 - 一旦设备安装上就委任的
 - 在服务操作之前或者进行中由运营商提供的
 - 根据需求，当用户接入网络或者使用服务才创建

LTE ID的分配或者提供是在图1中在相应的实体上用蓝色方块表示。

类型：LTE ID有一个属性类型，固定的（一旦创建就保持固定），临时的（当激活时发生变化）。被委任的或者分配都有固定的值，但根据需求，当用户接入网络或者使用服务时分配的是临时的值。

范围（其ID被唯一标识的内部）：每一个LTE ID在全世界/运营商网络/实体还是信道上是唯一标识的。
![这里写图片描述](http://img.blog.csdn.net/20150909191327692)
图1中，不同的ID根据LTE标识实际执行的地方来使用，例如，在哪个层，在哪个接口，或者在哪个物理区域。为了描述的方便，图1中的ID被分类展示在表1中。
首先，EPS实体的ID被分组为UE ID，ME ID，NE ID。这些EPS实体被分类为UE和NE。ME是UE的一部分，被从UE中分离，被分为一个独立的组。NE是由LTE运营商操作的网络实体，比如，MME，eNB，PGW。像IMSI/GUTI/S-TMSI/IP地址/C-RNTI/UE S1AP ID/UE X2AP ID这样子的ID标识一个用户的，属于UE ID组。像IMEI/IMEISV这样的ID标识一个设备的，属于ME ID组。
像MME的GUMMEI和MMEI，eNB的全球eNB ID和eNB ID，小区的ECGI和ECI，PGW的PGW ID，这些都属于NE ID组。像TAI和TAC这样的Location ID来标识用户附着的区域。
最后，像PDN ID（or APN），EPS承载ID，E-RAB ID，DRB ID，TEID，LBI这样的会话/承载ID是和用户数据传送有关，用来识别EPS会话和EPS承载。


这个文档，描述了在表1中灰色的UE和ME的LTE ID。第二个文档将涉及LTE NE ID和Location ID用来标识UE的位置区域。第三个文档将讨论EPS会话和承载ID。

表1，LTE ID的分类

ID group|LTE ID|相关的文档
----|-----
UE ID|IMSI, GUTI, S-TMSI, IP address (PDN address), C-RNTI, eNB UE S1AP ID,MME UE S1AP ID, Old UE X2AP ID, UE X2AP ID|LTE Identification I
ME ID|IMEI|LTE Identification I
NE ID|GUMMEI, MMEI, Global eNB ID, eNB ID, ECGI, ECI, P-GW ID|LTE Identification II
Location ID|TAI TAC|LTE Identification II
会话/承载ID|PDN ID(APN), EPS Bearer ID, E-RAB ID, DRB ID, TEID, LBI|LTE Identification III

# III. Identifiers for User Equipment (UE IDs)
LTE网络是全IP网络。因为这个特性，在LTE网络中的UEs共享无线和网络资源。LTE网络的EPS实体给每一个UE分配UE ID来识别不同的UE。因为UEs在不同层和接口共享资源，所以需要多种多样的UE ID。
在移动通信网络中最必要的ID是PLMN ID，用来识别特定网络的运营商。我们首先开始包含PLMN ID的UE ID的描述。

## 3.1 PLMN ID指示用户需要签约的网络
PLMN是运营商分配和操作的，用来为公众提供移动通信服务。PLMN ID通常用来识别用户需要签约的移动通信网络。它包含MCC和MNC，如图2所示。MCC的三个数字表示用户所在的移动网络所属的国家。每一个国家有一个或多个PLMN ID。MCC的分配需要ITU-T的授权，定义在ITU-T E.212 [3]。例如韩国的MCC是450.一个MNC标识移动通信网络的运营商，是由每一个国家分配的。例如，在韩国有三个移动运营商，SK电信，KT和LG U+，他们的MNC如图2所示。
![这里写图片描述](http://img.blog.csdn.net/20150909212334046)

## 3.2 IMSI:分配给签约用户的固定ID
IMSI（International Mobile Subscriber Identity）是全球为一个号码来标识一个签约用户。图3表示了IMSI的分配过程和IMSI的格式。一个IMSI是由指示签约用户网络的PLMN和由运营商分配的MSIN（Mobile Subscriber Identification Number）。IMSI长度最大15个数字。MSIN识别一个PLMN中的签约用户。
![这里写图片描述](http://img.blog.csdn.net/20150909212947495)
当用户购买一个USIM卡并签约一个移动网络，就有了一个内建的唯一的IMSI开始启用并和用户关联。IMSI是存储在手机的USIM卡中，IMSI和用户签约信息是由运营商提供给HSS和SPR。在提供之后，当UE attach LTE网络的时候，UE发送IMSI给移动网络。在接收到UE的IMSI之后，MME开始LTE网络的默认EPS承载和基本传输路径的建立（通过1）基于从UE接收到的IMSI，识别出签约用户的本地网络，2）选择持有用户签约信息的HSS，3）从HSS中下载信息）。

存放在USIM/HSS/SPR中的IMSI是不可移除的固定值。另一方面，存储在MME/SGW/PGW/PCRF中的IMSI，在UE初始附着过程建立默认EPS承载时是一个临时值，当默认承载终止时被移除。

## 3.3 IDs Used at MME: GUTI, S-TMSI and M-TMSI

IMSI是一个固定唯一的ID来标识签约用户。如果它经常暴露在无线链路上将会不安全。为了增强安全性，当UE附着在网络时MME给UE分配一个GUTI（Globally Unique Temporary Identifier）用来代替IMSI来标识UE。图4是GUTI的分配过程和格式。
![这里写图片描述](http://img.blog.csdn.net/20150910082700443)
GUTI分配：当UE第一次附着到LTE网络时，它使用IMSI请求接入网络并从网络获得GUTI。当UE再次附着网络时，UE使用GUTI代替IMSI。当再次附着到网络时，UE使用IMSI或者GUTI作为ID取决于下次更新时临时标识（TIN）设置的是什么。

一旦UE的初始附着过程和TAU过程是成功执行，MME分配和发送GUTI给UE，接着设置GUTI作为它的TIN。其后，当UE附着网络或者请求TAU更新时都使用GUTI代替IMSI。

GUTI格式：LTE运营商有一个或者多个MME组包含多个MME。因此MMEI（MME Identifier）是由代表MME组的MMEGI（MME Group Identifier）和代表组内一个MME的MMEC（ MME Code）组成。GUMMEI（Globally Unique MME Identifier）是在MMEI基础上加上PLMN。每一个MME每一个注册的用户分配M-TMSI，这是在MME中唯一的值，来保护签约用户的机密性。

GUTI是由GUMMEI和M-TMSI组成，是全球唯一的值，用来代替IMSI标识UE。不像IMSI有一个固定的值，GUTI是临时的值，当UE注册到LTE网络时由MME分配的。所以，当GUTI经常暴露在无线链路上也是保持安全的。S-TMSI由MMEC和M-TMSI组成，用来唯一标识MME组中的UE。如果运营商没有多个MME组时，可以使用比较短的S-TMSI来提高在无线链路上传输的有效性。

## 3.4 IP地址：连接PDN必须的ID
IP地址，也称为PDN地址，是当UE初始附着在LTE网络时LTE网络分配给UE的用来连接PDN。因为UE可以根据不同的服务通过LTE网络连接到多个PDN，所以LTE网络针对每一个UE连接到的PDN为UE分配不同的IP地址（例如两个IP地址用于两个连接的PDN，三个IP地址用于三个连接的PDN等等）。当IP数据包从LTE网络到PDN转发，或者从PDN接收数据包时，这些IP地址用来标示IP数据包发送或接收的UE。

IP地址固定或者动态地分配用于UE连接PDN。这两种分配方式称为静态IP分配和动态IP地址分配。
对于静态IP地址分配，运营商在签约的时候分配给UE一个固定的IP地址，并把这个UE的IP地址提供给HSS。不管UE附着的时间和地点，每次UE发起附着到PDN时，UE都被分配相同的IP地址。
对于动态的IP地址分配，PGW有一个IP地址池，每次UE执行初始附着到LTE网络时，UE被动态地从IP地址池中分配一个可用的IP地址。因此，每次UE初始附着LTE网络时，分配给UE的IP地址都不一样。

图5是动态IP地址分配的实例。它简单介绍了在初始附着过程，默认承载建立时，PGW动态分配临时IP地址的过程和在初始附着过程分配动态IP地址后，UE使用因特网服务的过程。
![这里写图片描述](http://img.blog.csdn.net/20150910091112896)

## 3.5 C-RNTI：在一个小区内分辨UE需要的ID
C-RNTI（Cell Radio Network Temporary Identifier）是在随机接入过程中由eNB分配的，并仅仅在当前的服务小区有效。在小区中的UE是使用C-RNTI来唯一标识的。当UE离开当前小区，通过随机接入过程移动到一个新的小区时，分配一个新的C-RNTI。图6是C-RNTI是怎么样分配的，和C-RNTI应用于哪一层。

在上行和下行链路，eNB负责无线资源的分配。它通过在PDCCH（Physical Downlink Control Channel）上广播C-RNTI通知在下一个TTI（Transmission Time Interval）哪一个UE可以使用无线资源。如果UE在它所接入小区的PDCCH信道上发现自己的C-RNTI，则UE就获知在在下一个上行或下行TTI上可以使用无线资源。

![这里写图片描述](http://img.blog.csdn.net/20150910092323414)
## 3.6 在S1-MME接口上需要成对的UE S1AP ID来分辨UE

S1AP层成功优酷S1-MME接口上eNB和MME之间的控制信息。在同一时间有多个UE和eNB保持连接。eNB和UE对应的MME交换所有的S1AP控制信息时使用相同的S1链路。所以，为了识别那个S1AP消息对应于哪一个UE，当eNB发送UE的第一个S1AP消息给MME时，eNB为每一个UE分配了一个ID（eNB UE S1AP ID）。同样的，MME通过多个S1链路和多个eNB交换S1AP信息。为了识别哪一个S1AP消息对应于哪一个eNB的哪一个UE，当MME发送第一个UE消息给eNB时分配一个ID（MME UE S1AP ID）。

在S1AP消息的第一个来回后，在S1-MME接口上交换的所有用户控制消息（S1AP）是使用一对UE S1AP ID来传输的（eNB UE S1AP ID, MME UE S1AP ID），目的是为了eNB和MME都能分辨出来哪一个S1AP消息对应于哪一个UE。然后，根据这对ID，MME可以找到接收到的S1AP消息是来自哪一个eNB的哪一个UE，并且eNB可能知道接收到S1AP消息来自哪一个UE。图7是UE S1AP ID的分配过程和UE S1AP ID应用在S1AP层。
![这里写图片描述](http://img.blog.csdn.net/20150910095355399)
## 3.7 在X2接口上需要成对的UE X2AP ID来区别UE
X2AP层处理在X2接口上两个相邻的eNB之间的控制信息。在两个相邻的eNB之间UE的切换时，X2AP消息使用相同的X2链路从UE发送到对端的eNB。第一次eNB（源eNB或者目的eNB）发送X2AP给对端eNB时，eNB为每一个UE分配ID，并使用这个ID发送消息来标识是哪一个UE的X2AP消息。源eNB在第一个到目的额NB的消息（切换请求消息）中分配一个Old eNB UE X2AP ID，目的eNB在第一个到源eNB的响应消息（切换请求确认消息）中分配一个New eNB UE X2AP ID。

在第一次来回信息交换后，所有的在X2接口上和切换相关的X2AP消息都使用成对的ID(Old eNB UE X2AP ID, New eNB UE X2AP ID)来交换，目的是为了源eNB和目的eNB都能识别出来哪一个X2AP消息对应于哪一个UE。图8是UE X2AP ID的分配过程和UE X2AP ID应用在X2AP层。
![这里写图片描述](http://img.blog.csdn.net/20150910100941929)

# IV. Identifiers for Mobile Equipment (ME IDs)

第四章将描述ME ID。在讨论ME ID之前先认识下UE和ME的关系。一个UE由ME和USIM（UMTS Subscriber identity Module）卡组成。ME可以分为TE（Terminal Equipment）和MT（Mobile Terminal）。MT是负责无线接入协议工作，而TE是负责MT控制功能工作。图9是这些功能组的组成。MT和TE是集成在手机里，但是在note PC上是分开的。
![这里写图片描述](http://img.blog.csdn.net/20150910101522931)
## 4.1 IMEI和IMEISV：ME拥有的固定ID
IMEI（International Mobile Equipment Identity）是为每一个ME分配的唯一值。IMEI是在ME出厂的时候被给出的，包含了和生产厂商的信息，型号，和ME的序列号。图10是IMEI的格式和IMEI使用的举例。IMEI是由TAC（Type Allocation Code）和SNR（Serial Number）和CD（Check Digit）组成。一个IMEI/SV是由TAC（Type Allocation Code）和SNR（Serial Number）和SVN（Software Version Number）组成。
TAC是由RBID和ME type ID组成，其中RBID是指示认证机构的RBID（Reporting Body Identifier），ME type ID代表生产厂商的名字和型号ID。序列号是由生产厂商所分配的。在图10的例子中，RBID=35标示ME是由BABT（British Approvals Board for Telecommunications英国电信认证委员会）批准的，ME ID=643205表示ME是由三星生产的智能手机。

运营商有数据库存储IMEI信息，使用这个数据库可以拒绝任何被报告被盗和丢失ME的接入尝试。
![这里写图片描述](http://img.blog.csdn.net/20150910104555713)

# V. Closing
在我们认识LTE技术之前，我们已经讨论了LTE标识。在这个文档，我们首先解释了UE和ME ID，接下来我们根据他们的范围/分配/属性分类和总结了这些ID，见表2。其他的ID将会在LTE identification II III中讨论。
![这里写图片描述](http://img.blog.csdn.net/20150910104916487)

# References
[1] Netmanias Technology Document, “LTE Network Architecture: Basic”, July 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5904
[2] 3GPP TS 23.003, “Numbering, addressing and identification”.
[3] ITU-T E.212, “The international identification plan for public networks and subscriptions”.
[4] GSMA TS.06, “IMEI Allocation and Approval Guidelines”.
[5] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.


