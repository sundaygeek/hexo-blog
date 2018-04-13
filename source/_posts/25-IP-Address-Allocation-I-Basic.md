---
title: 25-IP Address Allocation I - Basic
date: 2016-04-03 22:46:54
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=7246

# I. Introduction

lte是全IP网络。这就意味着交付所有用户流量时都是以IP数据包的形式，提供给用户always on IP connectivity的服务。当UE加入LTE网络，会给UE分配一个PDN地址让UE连接这个PDN，并建立默认承载。默认承载保持连接知道UE从LTE网络中detach掉。
默认承载对用户拥有的没有APN建立，为每一个APN分配独立的IP地址，一个IP地址可以为ipv4，ipv6，ipv4/ipv6三种类型。

这篇文件主要解释当用户初始接入网络时LTE网络怎么给用户分配IP地址的，并通过实例来解释。

在接下来的文档中我们将会涉及到两种类型的IP地址，这篇文档涉及到基本IP地址分配机制和过程，描述熊地址是怎样分配给用户的（用户处于一个位置，对网络执行多次初始attach）。接下来文档我们将学习不同类型的IP地址分配（用户在两个物理分割区域cityA和cityB执行初始附着）。
这篇文档组织如下：第二章，不同类型的IP地址分配--动态的和静态的，并讨论他们的特点。第三章和第四章将详细讨论动态和静态IP地址的分配过程。

# II. Types of IP Address Allocation

当UE初始附着到LTE网络，UE请求PDN连接。PGW分配一个IP地址供UE连接PDN，并在U默认承载建立时连接UE和PDN，转发给UE。使用这个IP地址，UE可以获得PDN提供的服务。
PGW分配IP地址有两种方式：动态的和静态的分配。对于动态分配，当UE每次接入网络时自动分配一个IP地址。对于静态分配，一旦用户签约后指定给UE一个固定的IP地址，并在每次UE接入网络时都分配这个指定的IP地址。图1比较两种方式的不同。
![这里写图片描述](http://img.blog.csdn.net/20151021121418827)

对于动态分配，网络PGW自动为UE选择IP地址。在PGW中网络运营商有一个IP地址池。的那个UE初始附着到LTE网络时，PGW动态为UE分配一个IP地址。所以每次UE初始附着到网络时分配的一个新的动态IP地址。
但是，对于静态分配，一旦UE签约网络之后网络运营商就分配了一个固定的IP地址。运营商在hss中和其他签约信息一起为UE分配了静态的IP地址。接着当UE初始附着到LTE网络时，PGW从hss中获得这个静态IP地址并转发给UE。所以在每次UE初始附着网络时给UE分配的都是特定的IP地址。

当在初始附着请求PDN连接时，UE通过使用协议配置选项PCO可以请求和外部协议/应用相关的协议数据（eg请求dns服务器地址，P-CSCF地址）。这篇文档将详细解释dns服务器地址和IP地址的请求。


# III. Dynamic IP Address Allocation

对于动态IP地址分配，PGW为UE保留IP地址池，并在UE初始附着到LTE网络时为其分配一个IP地址。
![这里写图片描述](http://img.blog.csdn.net/20151021122301279)

**PGW中提供IP地址**
在PGW中，分配了一个IP地址池，并且dns服务器IP地址已经分配了。
![这里写图片描述](http://img.blog.csdn.net/20151021134900737)

**动态IP地址分配过程**
用户打开UE，试图初始附着到LTE网络上。

**1）【UE->MME】请求PDN connectivity**
UE通过发送PDN connectivity request(PDN type=IPv4, PCO=DNS Server IPv4 Address Request)消息给MME请求PDN连接。这是，除了请求ipv4地址，也请求dns服务器地址。PDN connectivity request消息是一个ESM消息，但是这个消息是嵌入在attach request(IMSI，ESM Message Container)消息中传输的。

**2）~3）【MME->SGW->PGW】请求会话创建**
MME基于从hss接收到的签约配置文件，发送create session request(IMSI, PDN Type=IPv4, PDN Address=0.0.0.0, PCO=DNS Server IPv4 Address Request) 消息给SGW请求EPS会话创建。因为是动态IP分配，这个签约配置文件中不包含IP地址信息，在create sessionrequest消息中PDN地址域是0.0.0.0，从UE接受到的PCO信息也包含在PCO域中。

**4）【PGW】分配PDN地址和dns服务器地址**
PGW在检查PDN类型和PDN地址（0.0.0.0）之后发现需要分配ipv4地址。PGW从ipv4地址池中选择一个IP地址(e.g. UE IP=1.1.1.5)，并分配给UE。根据PCO域的请求，也分配相应的dns服务器IP地址。

**5）~6）【MME<-SGW<-PGW】对请求会话创建的响应**
作为对2-3步请求的响应，PGW发送create session response消息给MME。这个消息包含PDN地址域的UE IP地址，PCO域的dns服务器IP地址。

**7）【UE<-MME】请求默认承载上下文的激活**
MME给UE发送activate default EPS bearer context request(PDN Type=IPv4, PDN Address=UE IP(1.1.1.5), PCO={Primary DNS IP, Secondary DNS IP})消息来请求对默认承载上下文的激活。这个ESM消息包含dns服务器IP地址和UE IP地址，嵌入到attach accept消息中传输。

**8）【UE】获得动态IP地址使用PDN服务**
UE获得动态IP地址和dns服务器地址。UE和PGW之间建立默认承载。UE现在已经连接到PDN上，可以以动态IP地址来使用Internet服务。

# IV. Static IP Address Allocation

对于静态IP地址分配，当用户第一次签约网络时网络运营商为用户指定了一个UE IP地址，在hss中提供用户签约配置文件，并在用户每次初始附着到网络时分配一个存储在配置文件中的静态IP地址。详细的过程如图3所示。
![这里写图片描述](http://img.blog.csdn.net/20151021145233115)
**hss中提供IP**
在hss中，提供了每一个签约用户的签约配置文件。这个配置文件包括PDN类型，用于PDN连接的PDN地址。
![这里写图片描述](http://img.blog.csdn.net/20151021150651205)
**PGW中IP提供**
在PGW中，dns服务器IP地址已经设置了。
![这里写图片描述](http://img.blog.csdn.net/20151021150809355)

**静态IP地址分配的过程**
用户打开UE，试图初始化附着到LTE网络。

**1）【UE->MME】请求PDN连接**
UE通过发送PDN connectivity request消息(PDN type=IPv4, PCO=DNS Server IPv4 Address Request)消息给MME请求PDN连接。这时，除了IPv4地址，包括dns服务器地址也请求了。

**2）【MME->HSS】为了注册请求LTE网络**
MME通过发送update location request消息通知HSS，UE已经在MME的控制之下，请求在网络上注册UE。

**3）【MME<-HSS】转发签约配置文件**
HSS识别到UE已经在MME上注册了，通过发送update location answer(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1))消息来转发UE的签约配置文件。这个签约配置文件包含静态IP地址。

**4）~5）【MME->SGW->PGW】请求会话创建**
在从HSS接收到UE签约配置文件后，MME知道UE已经有一个额静态IP地址了。MME准备create session request消息(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO=DNS Server IPv4 Address Request)并发送给PGW。这时，这个消息在PDN地址域包含从hss接收到的静态IP地址，在PCO域包含从UE接收到的PCO信息。

**6）~7）【MME<-SGW<-PGW】对创建会话请求的响应**
作为对4-5步请求的响应，PGW和SGW发送create session response消息(IMSI, PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO={Primary DNS IP, Secondary DNS IP})给MME。这个消息在PDN地址域包含静态IP地址，在PCO域包含dns服务器地址。

**8）【UE<-MME】请求默认承载上下文的激活**
MME通过发送active default EPS bearer context request消息(PDN Type=IPv4, PDN Address=Static UE IP(1.1.1.1), PCO={Primary DNS IP, Secondary DNS IP})请求默认承载上下文的创建。这个ESM消息包含UE的静态IP地址和dns服务器地址，嵌入到attach accept消息中。

**9）【UE】获得静态IP地址来使用PDN服务**
UE获得静态IP地址和dns服务器IP地址。UE和PGW之间建立了默认承载。UE现在连接了PDN，可以通过静态IP地址使用Internet服务了。

# V. Closing

到目前为止，我们讨论了两种分配IP的方式：静态和动态。当用户试图初始附着到LTE网络时，LTE网络分配一个IP地址来连接到PDN上和默认承载。只要用户网络注册有效，PDN地址和默认承载一直保持有用，即使用户没有在使用服务。这种方式叫做always-on IP connectivity。
这个文档解释了IP地址分配的机制和过程。下面一个文章我们将讨论在不同的位置UE连接PDN时的情况。

# References
[1] Netmanias Technical Document, “LTE IP Address Allocation Schemes II: A Case for Two Cities”,
February 2014 (TBD)
[2] Netmanias Technical Document, “EMM Procedure 1. Initial Attach - Part 1. Cases of Initial Attach”,
December 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=6098
[3] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010