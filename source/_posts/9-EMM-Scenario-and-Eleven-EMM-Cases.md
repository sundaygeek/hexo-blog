---
title: 9-EMM Scenario and Eleven EMM Cases
date: 2016-04-03 22:18:53
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---

原文地址：http://www.netmanias.com/en/?m=view&id=techdocs&no=6002

![这里写图片描述](http://img.blog.csdn.net/20150909103728607)
NOTE：初始状态，UE A是关机的，并且在网络中没有UE A的UE上下文存在。


----------


### EMM case 1，Initial Attach（初始附着）
![这里写图片描述](http://img.blog.csdn.net/20150909103827004)
UE状态：“EMM-Deregistered，ECM/RRC-Idle”-->“EMM-Registered，ECM/RRC-Connected”

 - UE A开机，UE A进入“EMM-Deregistered，ECM/RRC-Idle”并试图接入网络。
 - 在UE A和MME中没有UE A的UE上下文
 - 在同步到一个小区之后，UE A用IMSI作为UE ID发送Attach Request给MMT来发起initial attach过程
 - 在成功完成initial attach之后，UE A进入“EMM-Registered，ECM/RRC-Connected”状态并使用注册的服务。


----------


### EMM case 2，Detach（去附着）
![这里写图片描述](http://img.blog.csdn.net/20150909104428534)
UE状态：“EMM-Registered，ECM/RRC-Connected”-->“EMM-Deregistered，ECM/RRC-Idle”

 - UE A在“EMM-Registered，ECM/RRC-Connected”状态下从网络去附着。
 - 根据detach触发的原因，有三种类型的detach过程（UE发起的，MME发起的和HSS发起的）。
 - 一旦成功完成了Detach过程之后，UE A进入"EMM-Deregistered，ECM/RRC-Idle”状态


----------


### EMM case 3，由于用户不活动释放S1
![这里写图片描述](http://img.blog.csdn.net/20150909105015317)
UE状态：“EMM-Registered，ECM/RRC-Connected”-->“EMM-Registered，ECM/RRC-Idle”

 - 在“EMM-Registered，ECM/RRC-Connected”状态下在一段时间内UE A没有使用服务。
 - eNB检测到用户的不活动，释放S1承载和S1信令连接。
 - 在成功完成S1释放过程后，UE A进入“EMM-Registered，ECM/RRC-Idle”状态。


----------


### EMM case 4，由于新的业务发起服务请求
![这里写图片描述](http://img.blog.csdn.net/20150909105754350)
UE状态：“EMM-Registered，ECM/RRC-Idle”--->“EMM-Registered，ECM/RRC-Connected”

 - UE A在“EMM-Registered，ECM/RRC-Idle”状态下产生了新的业务。
 - 新的业务可以由UE A产生也可以由网络产生，并发起服务请求过程。
 - 在成功完成服务请求过程之后，UE A进入“EMM-Registered，ECM/RRC-Connected”状态。

----------

### EMM case 5，TAU
![这里写图片描述](http://img.blog.csdn.net/20150909110642528)
UE状态：“EMM-Registered，ECM/RRC-Idle”--->“EMM-Registered，ECM/RRC-Connected”--->“EMM-Registered，ECM/RRC-Idle”

 - UE A在“EMM-Registered，ECM/RRC-Idle”状态，且周期性TAU定时器（T3412）超时。
 - UE A执行周期性TAU过程，UE A建立到MME的ECM连接，发送TAU Request消息并转移到“EMM-Registered，ECM/RRC-Connected”状态。
 - 在成功完成周期性TAU过程之后，释放ECM连接并且UE A进入“EMM-Registered，ECM/RRC-Idle”状态。


----------
### EMM case 6，没有TAU的切换
![这里写图片描述](http://img.blog.csdn.net/20150909111502547)
UE状态：“EMM-Registered，ECM/RRC-Connected”-->“EMM-Registered，ECM/RRC-Connected”

 - UE A在“EMM-Registered，ECM/RRC-Connected”状态，移动到一个新的小区，并且检测到这个新进入的TA包含在TAI列表中。
 - 执行切换到新的小区，但是不需要执行TAU过程。
 - 在成功完成切换之后，UE A保持在“EMM-Registered，ECM/RRC-Connected”状态。


----------
### EMM case 7，没有TAU的小区重选
![这里写图片描述](http://img.blog.csdn.net/20150909112029992)
UE状态：“EMM-Registered，ECM/RRC-Idle”-->“EMM-Registered，ECM/RRC-Idle”

 - UE A在“EMM-Registered，ECM/RRC-Idle”状态，移动到一个新的小区，并且检测到这个新进入的TA包含在TAI列表中。
 - 执行重选到新的小区，但是不需要执行TAU过程。
 - 在成功完成重选过程之后，UE A保持在“EMM-Registered，ECM/RRC-Idle”状态。


----------
### EMM case 8，包含TAU的切换
![这里写图片描述](http://img.blog.csdn.net/20150909112237420)
UE状态：“EMM-Registered，ECM/RRC-Connected”-->“EMM-Registered，ECM/RRC-Connected”

 - UE A在“EMM-Registered，ECM/RRC-Connected”状态，移动到一个新的小区，并且检测到这个新进入的TA不包含在TAI列表中。
 - 执行切换到新的小区，并且需要执行TAU过程。
 - 在成功完成切换之后，UE A保持在“EMM-Registered，ECM/RRC-Connected”状态。


----------
### EMM case 9，包含TAU的小区重选
![这里写图片描述](http://img.blog.csdn.net/20150909112409558)
UE状态：“EMM-Registered，ECM/RRC-Idle”-->“EMM-Registered，ECM/RRC-Connected”-->“EMM-Registered，ECM/RRC-Idle”

 - UE A在“EMM-Registered，ECM/RRC-Idle”状态，移动到一个新的小区，并且检测到这个新进入的TA不包含在TAI列表中。
 - 执行重选到新的小区，并且需要执行TAU过程。UE A建立到MME的ECM连接并发送TAU Request消息，转移到“EMM-Registered，ECM/RRC-Connected”状态。
 - 在成功完成TAU更新过程之后，UE A进入“EMM-Registered，ECM/RRC-Idle”状态。

----------
### EMM case 10，移动到另一个城市
![这里写图片描述](http://img.blog.csdn.net/20150909112737616)
UE状态：“EMM-Registered，ECM/RRC-Connected”-->“EMM-Registered，ECM/RRC-Idle”-->“EMM-Deregistered，ECM/RRC-Idle”

 - UE A从城市1（正在服务或者在idle状态）移动到城市2。UE A移出了LTE的覆盖区外，并且从网络detach。
 - 在detach过程之后，UE A进入“EMM-Deregistered，ECM/RRC-Idle”状态。

----------
### EMM case 11，在另一个城市初始附着
![这里写图片描述](http://img.blog.csdn.net/20150909113113163)
UE状态：“EMM-Deregistered，ECM/RRC-Idle”-->“EMM-Registered，ECM/RRC-Connected”

 - UE A在“EMM-Deregistered，ECM/RRC-Idle”状态，进入到城市2，并且检测到一个新的LTE小区。
 - UE A用GUTI作为UE ID发送Attach Request小区给新的MME发起初始附着过程。
 - 在成功完成初始附着过程之后，UE A进入“EMM-Registered，ECM/RRC-Connected”状态。


----------
![这里写图片描述](http://img.blog.csdn.net/20150909113559035)

# References
[1] Netmanias Technical Document, “LTE EMM and ECM State”, February 2013,
http://www.netmanias.com/bbs/zboard.php?id=1x_TechdocsForum_4G
[2] 3GPP TS 24.301, “Non-Access-Stratum (NAS) Protocol for Evolved Packet System (EPS); Stage 3”.
[3] 3GPP TS 23.401, “General Packet Radio Service (GPRS) Enhancements for Evolved Universal Terrestrial Radio Access Network
(E-UTRAN) Access“.
[4] NMC Consulting Group Report, “E2E LTE Network Design”, August 2010.

