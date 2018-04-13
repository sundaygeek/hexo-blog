---
title: 26-LTE IP Address Allocation II - A Case for Two Cities
date: 2016-04-03 22:47:04
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=7257

# I. Introduction

在之前的文档我们已经覆盖了基本IP地址分配过程，解释了在用户同一个地点不同时间接入网络时LTE网络是怎么分配IP地址的。这个文档有一点不同，学习用户在不同物理位置上接入网络时的分配IP过程。和之前的文档意一样，要讨论两种分配方式：静态和动态的。另外我们要学习UE和PDN之间的连接PGW是怎么选择的，默认承载路径是怎么设置的。

对于动态分配方式，不管UE接入到哪个城市，LTE网络都分配一个动态的IP地址给UE，并且UE可以使用这个IP地址通过PGW连接到PDN。这个IP地址不管用户的接入位置和之前文档介绍的分配方式一样。但是对于静态分配方式，网络分配一个固定的IP地址，允许UE通过一个指定的PGW接入到PDN，不管UE接入到哪里。这个过程和之前文档根据用户的签约配置文件分配固定IP地址的方式一样。但是不同点在于默认承载路径要在城市内或者两个物理城市之间设置，现在有一个指定的PGW。

这个文档组织如下：第二章，讨论不同IP地址分配类型--静态和动态的以及他们的特点。第三章和第四章将提供动态和静态分配的详细过程。

# II. Types of IP Address Allocation

下面，我们将使用两个城市的模型来描述分配过程。在这个场景中，我们假设在每一个城市（city1和city2）都包含MME和SGW和PGW。每一个PGW有自己的IP地址池，只有city1有HSS。UE仅仅使用一个PDN服务（Internet）。当请求PDN连接时，UE请求dns服务器地址和PDN地址。详细的场景如下：

 1. 在city1，用户打开UE，接入LTE网络，使用PDN服务
 2. 在使用Internet服务后，用户关闭UE，并移动到city2
 3. 一旦到达city2，用户打开UE，接入LTE网络，使用PDN服务。

在city1或者city2初始附着时，UE向MME请求PDN连接。一旦请求，MME通过UE接入的PDN选择一个PGW，这个PGW决定IP地址。当默认承载建立之后PGW转发IP地址给UE，UE可以使用这个IP地址来使用PDN服务。

在这个两个城市的模型中，IP地址以两种类型分配--动态和静态。动态分配在第三章讨论，静态在第四章讨论。每一种方式的IP地址是谁选择和PGW选择的方式是不同的，如下表所示：

![这里写图片描述](http://img.blog.csdn.net/20151021164240472)

# III. Dynamic IP Address Allocation

对于动态分配方式，IP地址不论用户什么时候执行初始附着到网络都是动态分配的。MME通过用户接入的PDN选择一个PGW，并要求建立一个EPS会话。PGW动态选择一个IP地址供UE使用来接入PDN。图1描述了在两个城市模型中动态IP地址分配的情况。
![这里写图片描述](http://img.blog.csdn.net/20151021164533729)

**PGW中IP地址提供**
在PGW1和PGW2中，IP地址池包含待分配的IP地址，dns服务器地址已经由网络运营商提供了。
![这里写图片描述](http://img.blog.csdn.net/20151021164819551)
**IP地址获取和PDN连接**
用户打开UE，向当地的MME请求PDN连接。从hss下载的签约配置文件中，MME知道UE不是使用静态IP地址。所以MME选择一个本地的PGW（PGW1 PGW2），接着从这个PGW IP地址池中选择一个可用的IP地址分配给UE。当默认承载建立时，UE从本地PGW中获得dns服务器地址和UE IP地址，接着，通过本地PGW连接PDN使用Internet服务。

**动态IP地址分配过程**
在city1或者city2动态IP地址分配的过程在之前的文档已经详细叙述过了。

# IV. Static IP Address Allocation

对于静态IP地址分配，i）UE使用的IP地址，ii）PGW的ID，这些都已经作为签约配置文件在PDN中提供了。不管用户在哪里接入LTE网络，MME选择指定的PGW，这个PGW分配存储存在用户签约配置文件中的静态IP地址。不管初始接入的位置，UE一直会分配相同的固定IP地址，并且在UE和指定PGW之间建立默认承载。图2描述了静态IP在两个城市模型中是怎么分配的。

![这里写图片描述](http://img.blog.csdn.net/20151021181558154)

**hss中提供IP**
在hss中每一个用户提供签约配置文件。没有签约配置文件包含PDN类型，PDN地址和PGW ID。
![这里写图片描述](http://img.blog.csdn.net/20151021181724863)

**PGW提供IP**
在PGW中，分配给UEdns服务器地址。
![这里写图片描述](http://img.blog.csdn.net/20151021181817194)

**IP地址获取和PDN连接**
在开机后，UE1向本地MME请求PDN连接。MME从HSS中下载UE的签约配置文件，并确认UE有一个静态IP地址和指定的PGW。基于这些信息，MME开始创建EPS会话和建立EPS承载。
这种方式，无关UE1是在哪里接入LTE网络，i)分配相同的静态IP地址，ii）建立连接指定PGW（PGW1）的默认承载路径，iii）UE1通过PGW1连接到PDN。这个默认承载路径是每一一个城市是这样的：
city1： UE1- eNB-SGW1-PGW1
city2：UE1-eNB-SGW2-PGW1

**静态IP地址分配过程**
图3描述了在两个城市模型中静态IP地址分配的过程。
![这里写图片描述](http://img.blog.csdn.net/20151021182244045)

**在city1**

**1）【UE1->MME1】请求PDN连接**
在city1，开机后，UE1通过发送PDN connectivity request消息(PDN Type=IPv4, PCO=DNS Server IPv4 Address Request) 来请求MME1建立PDN连接。这时，除了包含UE的IPv4地址还包含请求的dns服务器地址。

**2）【MME1->HSS】请求LTE网络注册**
MME1通过发送update location request消息通知HSS，UE1已经在控制之下，并且在网络上注册UE1。

**3）【MME1<-HSS】转发签约配置文件** 
HSS识别到UE1已经在MME上注册了，通过发送update location answer(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1))消息来转发UE1的签约配置文件。这个签约配置文件包含静态IP地址和PGW ID。

**4）~5）【MME1->SGW1->PGW1】请求会话创建** 
在从HSS接收到UE签约配置文件后，MME1知道UE1已经有一个静态IP地址了。MME1准备create session request消息(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO=DNS Server IPv4 Address Request)并发送给PGW1。这时，这个消息在PDN地址域包含从hss接收到的静态IP地址，在PCO域包含从UE接收到的PCO信息。

**6）~7）【MME1<-SGW1<-PGW1】对创建会话请求的响应** 
作为对4-5步请求的响应，PGW1发送create session response消息(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO={Primary DNS IP, Secondary DNS IP})给MME1。这个消息在PDN地址域包含静态IP地址，在PCO域包含dns服务器地址。

**8）【UE1<-MME1】请求默认承载上下文的激活** 
MME1通过发送active default EPS bearer context request消息(PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO={Primary DNS IP, Secondary DNS IP})请求默认承载上下文的创建。这个ESM消息包含UE的静态IP地址和dns服务器地址，嵌入到attach accept消息中。

**9）【UE1】获得静态IP地址来使用PDN服务** 
UE1获得静态IP地址和dns服务器IP地址。UE和PGW之间(UE1 – eNB – S-GW1 – P-GW1)建立了默认承载。UE现在连接了PDN，可以通过静态IP地址使用Internet服务了。

在使用Internet服务之后，UE1关机，从LTE网络detach，一旦到达city2，UE1重选开机。

**在city2**

**1）【UE1->MME1】请求PDN连接**
在city1，开机后，UE1通过发送PDN connectivity request消息(PDN Type=IPv4, PCO=DNS Server IPv4 Address Request) 来请求MME2建立PDN连接。这时，除了包含UE的IPv4地址还包含请求的dns服务器地址。

**2）【MME2->HSS】请求LTE网络注册**
MME1通过发送update location request消息通知HSS，UE1已经在控制之下，并且在网络上注册UE1。

**3）【MME2<-HSS】转发签约配置文件** 
HSS识别到UE1已经在MME2上注册了，删除在MME1中的UE上下文，通过发送update location answer(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1))消息来转发UE1的签约配置文件。这个签约配置文件包含静态IP地址和PGW ID。

**4）~5）【MME2->SGW2->PGW1】请求会话创建** 
在从HSS接收到UE签约配置文件后，MME2知道UE1已经有一个静态IP地址了。MME1准备create session request消息(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO=DNS Server IPv4 Address Request)并发送给PGW1。这时，这个消息在PDN地址域包含从hss接收到的静态IP地址，在PCO域包含从UE接收到的PCO信息。

**6）~7）【MME2<-SGW2<-PGW1】对创建会话请求的响应** 
作为对4-5步请求的响应，PGW1发送create session response消息(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO={Primary DNS IP, Secondary DNS IP})给MME2。这个消息在PDN地址域包含静态IP地址，在PCO域包含dns服务器地址。

**8）【UE<-MME】请求默认承载上下文的激活** 
MME2通过发送active default EPS bearer context request消息(PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO={Primary DNS IP, Secondary DNS IP})请求默认承载上下文的创建。这个ESM消息包含UE的静态IP地址和dns服务器地址，嵌入到attach accept消息中。

**9）【UE】获得静态IP地址来使用PDN服务** 
UE1获得静态IP地址和dns服务器IP地址。UE和PGW之间(UE1 – eNB – S-GW2 – P-GW1)建立了默认承载。UE现在连接了PDN，可以通过静态IP地址使用Internet服务了。

# V. Closing

当UE试图初始附着到LTE网络，LTE网络根据用户签约的服务提供PDN连接，一旦连接上，网络保持这个连接直到用户从网络detach。这就意味着网络分配一个IP地址来使用。PDN连接是为每一个PDN提供的。所以IP地址也是为每一个PDN提供的。

我们目前已经学习到LTE网络在两个城市模型中是怎么分配IP地址的。

# References
[1] Netmanias Technical Document, “LTE IP Address Allocation Schemes I: Basic”, February 2015,
http://www.netmanias.com/en/?m=view&id=techdocs&no=7246
[2] Netmanias Technical Document, “EMM Procedure 1. Initial Attach - Part 1. Cases of Initial Attach”,
December 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=6098
[3] 3GPP TS 23.401, “General Packet Radio Service (GPRS) enhancements for Evolved Universal
Terrestrial Radio Access Network (E-UTRAN) access”
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010