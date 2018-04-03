---
title: '3-LTE Identification II: NE and Location Identifiers'
date: 2018-04-03 21:37:33
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
    
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=5906

# I. Introduction
在之前的文档，LTE identification I介绍了UE和ME的标识。这篇文档，将介绍网络设备（Network Equipment）和Location ID。在这篇文章中被标识的网络设备包括eNB，MME，PGW，都是EPS实体。另外小区（eNB下由一个天线覆盖的区域）和这些网络设备一起讨论。Location ID包括所有标识UE位置的ID。UE在网络中是以TA级别注册的。所以，在这篇文档中TA相关的ID和Location ID一起讨论。

在第二章和第三章，NE ID和Location ID基于分类分别讨论。在第四章，总结这些ID的共同特征。

这篇文档设计的ID的范围和类型在图1和表1中展示。如果运营商的策略需要eNB报告UE（属于这个eNB）的位置，在方框中的ID（ECGI，TAI）会从eNB到PCRF传输。

![这里写图片描述](http://img.blog.csdn.net/20150910110844615)

![这里写图片描述](http://img.blog.csdn.net/20150910110909495)

# II. Identifiers for Network Equipment (NE IDs)
这章描述LTE 网络设备相关的ID。属于NE ID组的ID有MME的GUMMEI和MMEI，eNB的全球eNB ID和eNB ID，小区的ECGI和ECI，PGW的PGW ID。LTE NE ID被分为两类，一类是有PLMN ID，一类是没有PLMN ID，取决于他们是在PLMN内或者是全球的唯一标识。

## 2.1 标识MME的ID： GUMMEI, MMEI, MMEGI and MMEC
MME处于E-UTRAN和EPC之间。它是关键性的控制设备，它通过和HSS交换控制信号允许UE附着到LTE网络上，在HSS中包含UE的用户签约信息。它在核心网侧通过和SGW（或PGW）交换控制信号支持承载的管理，在E-UTRAN侧通过和eNB交换控制信号支持UE的移动性。通常，LTE运营商把多个MME分为一组，把他们作为MME组来操作。MME ID用来标识MME，是当MME安装时由运营商分配的。

MMEI用来标识在运营商的网络中的MME（比如，A LTE运营商的网络管理人员需要标识在A LTE网络中的MME）。GUMMEI包含PLMN ID和MMEI，是在运营商外部用来识别MME的（比如，当C操作A和B LTE运营商的网络时，C需要标识在A LTE网络中的MME）。MME组是由运营商构建的，MMEI包含MMEGI和MMEC，MMEGI代表MME组，MMEC代表一个组内的特定的MME。图2是MME相关的ID和他们的格式。
![这里写图片描述](http://img.blog.csdn.net/20150910114907732)
## 2.2 标识eNB的ID：eNB ID和Global eNB ID
eNB ID是用来标识运营商网络内的一个eNB，而Global eNB ID是由PLMN ID和eNB ID组成，是用来在网络外面标识一个eNB。图3是eNB和小区相关的ID和他们格式。为了标识属于一个eNB的小区，通过在eNB ID和Global eNB ID上加上一个小区ID。

eNB ID和小区ID是在eNB安装时由网络运营商分配的。一旦安装，eNB开始和MME之间建立S1连接的过程。当eNB请求MME 建立S1连接时，eNB报告Global eNB ID/所支持的TA/所支持的CSG（Closed Subscriber Group）。CSG是一种只对一部分签约用户开放的小区，它是由单一或者一些小区组成。MME发送服务的GUMMEI给eNB作为对S1连接建立请求的响应，并提供MME池的信息。
![这里写图片描述](http://img.blog.csdn.net/20150910120120794)

## 2.3 识别PGW的ID：PGW ID
当一个UE尝试附着在LTE 网络上，LTE网络提供给UE PDN连接。为了在UE和PDN之间建立PDN连接，MME需要知道UE连接的PDN和UE附着的PGW。默认的PDN作为签约用户的配置文件在HSS中已经提供了。所有，MME只需要从HSS中下载这些信息就可以了。对于PGW，有两种方式分配固定分配（由网络运营商提供，在HSS中作为签约用户的配置文件）和动态分配（根据运营商提供的PGW选择策略，MME选择一个PGW）。对于固定分配，HSS把PGW ID通知给MME，所以MME可以请求PGW建立PDN连接。

PGW ID用来标识PGW，可以是IP地址或者FQDN（Fully Qualified Domain name全称域名）格式。图4是依据固定分配策略分配的PGW ID和格式。举例，UE的默认PDN中一个PDN1用于因特网服务，一个PDN2用于语音服务。当UE发起接入到LTE网络，MME向HSS请求UE的签约信息，HSS通知MME 1）UE的默认PDN是PDN1（因特网）和PDN2（IMS），2）PGW连接PDN1的是PGW 1，连接PDN2的是PGW 3。MME接着通过PGW 1在UE和因特网之间建立PDN连接，通过PGW 3在UE和IMS之间建立PDN连接。
对于根据动态分配策略的PGW ID分配，MME从DNS中获取PGW IP地址列表，从列表中选择一个，然后请求PGW建立PDN连接。如果PGW列表是由DNS提供，MME按照PGW选择策略从列表中选择一个。

![这里写图片描述](http://img.blog.csdn.net/20150910122035954)

# III. Identifiers for UE Location (Location IDs)
MME负责管理UE的移动性，所以必须根据UE的位置来更新信息。如果UE在激活状态并且使用服务，UE的位置在LTE网络中是小区级别已知的。如果UE是在空闲状态并没有使用服务时，UE的位置则是TA级别已知的。因为在第二章已经介绍了小区ID，所以这一章只介绍TA ID。

## 3.1 识别UE位置的ID：TAC和TAI
UE在TA级别注册到网络。MME在TA级别定位空闲状态的UE。标识TA的ID是TAC(Tracking Area Code)和TAI (Tracking Area Identifier)。TAC用来在一个运营商网络中标识一个TA。TAI是包括PLMN ID和TAC，用来唯一的标识一个TA。图5是TA ID不同类型和格式。
![这里写图片描述](http://img.blog.csdn.net/20150910133007617)
当UE初始附着到一个LTE网络上，UE通过MME注册到网络上。在初始附着时，MME为UE分配一个TAI列表，并保持对UE位置的更新。UE把自己的新位置通知给MME，并当离开注册的TA时请求TA更新。用这种方法，MME就知道UE当前所待在的TA，并保存UE更新过的TAI列表。如果UE移动到包含在TAI列表中的TA区域，UE不会请求TA更新。但是，如果当前TA的更新周期超时，UE需要把自己当前的TA通知给MME，即使UE待在的TA包含在TAI列表中，让MME知道UE可以接收数据。

当在LTE网络有到达UE的数据，MME需要知道UE的位置来发送数据。如果UE是在激活状态，MME知道UE待在哪一个小区，因此可以容易地转发数据。但是如果UE在空闲状态，MME不知道UE待在哪一个具体的小区。所以，MME搜索UE最后一次报告的TA。空闲状态下的UE会在特定的周期醒来检查是否有寻呼消息。如果发现被寻呼了（通过检查在寻呼消息中的S-TMSI），它会相应寻呼消息来接收数据。TA区域越大意味着能容易地找到UE的概率越大。但是，在同一时间，TA区域越大意味着需要寻呼更多的eNB，因此需要更多的信令消耗。怎么分配TAI列表是一个优化的问题。

eNB通过安装时服务开通消息知道它属于哪个MME和TA。eNB中的每一个小区在系统消息中广播自己的小区ID（ECI，ECGI）和TA信息（TAC，TAI）。尝试接入新小区的UE通过监听广播系统消息判断新的小区是否和当前的小区有不同的TA，它的TA需要改变，或者新小区在UE的当前TA。
图5展示了分配TA列表的UE的行为。在图中，eNB1是在TA1，eNB在TA2，eNB3和eNB4是在TA3（一个TA可以包括几个小区和eNB）。UE1注册到MME1，被分配了{TAI1, TAI2}的TAI列表。UE2注册到MME2，被分配了{TAI2, TAI3}的TAI列表。在UE1接入到eNB1后变为空闲状态，因为它沿着点线移动（eNB1 ->eNB2->eNB3）,UE1的行为描述如下（TA在小区级别检测，但，当在同一个eNB下不同小区之间转移不在这里讨论）：

 - 在eNB1：UE当前的TA是TA1
 - 当从eNB1移动到eNB2：UE1当尝试接入新小区时，从新小区广播的系统信息知道新小区是TA2。UE检查TAI列表，发现TA2包含在TAI列表，所以，不需要请求TA更新。
 - 当从eNB2移动到eNB3：UE1当尝试接入新小区时，从新小区广播的系统信息知道新小区是TA3。UE检查TAI列表，发现TA3不包含在TAI列表，所以，需要发送TAU请求消息到MME来更新自己的位置。

# IV. Closing
我们在之前的文档知道了LTE ID中的UE ID和ME ID，在当前文档学习了NE ID和Location ID。表2总结了这篇文档涉及的ID，其他的ID将会在第三篇文档中介绍。
![这里写图片描述](http://img.blog.csdn.net/20150910140152636)

# References
[1] Netmanias Technical Document, “LTE Identification I: UE and ME Identifiers”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5905
[2] Netmanias Technology Document, “LTE Network Architecture: Basic”, July 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5904
[3] 3GPP TS 23.003, “Numbering, addressing and identification”.
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.


