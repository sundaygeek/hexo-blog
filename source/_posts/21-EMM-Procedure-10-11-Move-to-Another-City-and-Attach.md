---
title: 21-EMM Procedure - 10 & 11. Move to Another City and Attach
date: 2016-04-03 22:35:50
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6354

# I. introduction

随着UE的移动当服务小区的信号强度变弱时UE试图进行切换或者小区重选。如果在UE移动的周边没有邻区，信号强度将会逐步减弱直到UE最终从网络detach。接着一旦达到LTE覆盖区域，UE重选通过小区选择初始注册到网络。

这篇文档讨论当UE移动到一个LTE覆盖范围之外从网络detach，并移动到另外一个LTE网络重新attach到网络。

![这里写图片描述](http://img.blog.csdn.net/20151019090844653)

EMM  case-10 move to another city
EMM case-11 initial attach in another city
这两个EMM case，本文档假设移动运营商：

 - 在不同城市服务
 - 只有LTE-only网络，没有2/3G
 - 每一个城市有MME SGW PGW
 - HSS PCRF SPR仅仅安装在一个城市
 - 所有的MME通过S10接口

图1显示了city1和city2，车里的用户从city1移动到city2.
在EMM case10中，随着UE离开city1，UE从网络detach，移动出LTE覆盖范围。不论是connected 模式下使用服务还是在idle模式下驻留在服务小区，随着UE离开city1，UE被MME网络detach掉。

在EMM case11中，随着在city2 UE移动进入LTE覆盖范围，UE再次attach到网络。不像之前文档的情况，当UEattach 新的MME时，旧MME已经包含了UE上下文。UE初始附着到新MME上时就可以使用旧MME分配的UE ID。这样，UE就可以从detach 状态转换到connected状态。

这篇文档组织如下：第二章和第三章讨论EMM case10和11的过程。第四章将描述在这个过程前后EPS实体信息的变化。

# II. EMM Case 10. Move to Another City

##  2.1 在connected模式下移动
在图2中，我们看到当在city1服务的UE是怎么移出LTE覆盖范围并从网络detach的。

![这里写图片描述](http://img.blog.csdn.net/20151019092345531)
![这里写图片描述](http://img.blog.csdn.net/20151019092413038)

**1）【UE->eNB】测量报告**
随着UE离开city1，服务小区的信号强度变弱。随着A2事件的激活，UE给eNB发送measurement report消息通知服务小区的信号强度。

**2）【eNB】没有邻区来切换**
enB没有找到供UE切换的邻区。

**3）【eNB->MME】错误指示**
因为糟糕的通信质量，在无线接口上传送失败。eNB通过发送error indication(Cause=Failure in the Radio Interface)给MME通知这个失败。如果eNB发现和UE之间很难保持最低的质量，eNB将给MME发送UE context release request消息。

如果UE因为糟糕的通信质量丢失了RRC连接，UE试图RRC连接重建。如果这个质量的衰减是暂时的，为了正常的无线通信允许RRC进行重建。但是服务提供会中断。如果这个质量衰减是持续的，RRC连接重建将会持续失败，最终导致连接丢失。

假设i）当eNB和UE之间的RRC连接还持续有效，eNB给MME发送error indication。ii）因为UE当前服务小区位于城市边界，并且没有邻区切换，MME决定detach掉UE。所以由MME触发detach过程。

**4）MME发起detach**
MME通过发送detach request消息执行detach过程。这里的过程和之前文档中介绍的MME发起显式detach过程是一样的。MME存储UE的GUTI和NAS安全上下文，结束EPS会话，释放S1信令，并转移到detach状态。UE存储自己的GUTI和NAS安全上下文，删除EPS会话承载上下文，并转移到detach状态。

##  2.2 在idle模式下移动

idle模式下的UE会周期性的通过TAU报告自己的位置给网络。当UE有电话或者数据到来，MME不知道idle模式下的UE是否可达的。为了这个原因，idle状态下的UE即使处于TAI列表中的TA也需要周期性的报告自己的位置，这样网络可以知道UE是否可达的。MME有一个**TAU定时器T3412, 手机可达定时器，和隐式detach定时器**。TAU定时器的值是在UE初始附着到网络时通过attach accept消息转发给UE的，或者当UE做TAU request时通过TAU accept消息发给UE的。

TAU定时器默认是54分钟，如果MME设置这个值为0，UE就不激活TAU 定时器，并不执行周期性TAU。当UE从connected状态到idle状态转换时UE的TAU定时器激活。当这个定时器超时，UE转移到connected状态给MME发送TAU request消息通知UE是可达的，接着转移到idle状态，并重启TAU定时器。UE在从idle状态到connected状态转移时停止这个定时器，或者从网络detach时停止定时器。
如果MME为了UE超时设置的TAU定时器，MME立刻接收到了TAU request消息，通过这个消息MME可以跟踪UE的位置。接着，MME分配新的TAI列表，并重启TAU定时器。也就是说网络会在每一个TAU定时器周期结束时检查UE是否可达，并设置paging proceed flag为1，标示UE是可达的。

如果UE有任何问题，比如UE在T3412定时器超时时在一个阴影区域并不可达，UE在T3412超时时发起TAU request，就导致不能给MME通知自己的位置。当TAU失败时UE需要重新尝试。所以，如果UE很快从阴影区域移出，接着重选尝试TAU请求成功了。但是如果UE一直待在阴影区域，TAU 请求过程一直失败。

网络使用手机可达定时器来检查UE是否可达。和T3412 TAU定时器相比，有稍微更大的值，默认是T3412+4分钟。当在ECM连接释放时这个定时器开始，当一个新的ECM连接建立时停止定时器。

当手机可达定时器超时，MME知道UE在已经移除LTE服务区域，但是不知道移除覆盖状态需要持续多长时间。所以为了不立即删除UE上下文，MME清除PPF flag并开启隐式detach定时器。当PPF flag清除时，UE是本地detach的。也就是说当隐式detach定时器运行时，网络仍旧保持UE上下文没有删除，但是MME不会寻呼UE。即使当SGW有到达UE的电话或者数据包，SGW给MME发送downlink data notification时，MME拒绝这个消息。

当UE发送NAS消息，建立ECM连接，隐式detach定时器停止。如果MME在隐式detach定时器超时不能定位UE，MME相信UE已经长期到了LTE覆盖范围外，并从网络把UEdetach掉。现在网络中的UE上下文删除。

图3描述了UE在city1服务小区上驻留，随着移出LTE覆盖从网络detach的过程。

![这里写图片描述](http://img.blog.csdn.net/20151019101001669)

**1）【MME】TAU定时器T3412超时**
UE设定的TAU定时器超时，MME没有收到从UE的TAU request消息，并必须检查UE是否可达

**2）【MME】手机可达定时器超时**
UE的手机可达定时器也超时了。MME相信UE是在覆盖区外的状态，并清除PPF flag并开启隐式detach定时器。分配给UE的EPS承载安全上下文资源保持有效，但是MME不会寻呼UE。

**3）【MME】手机隐式detach定时器超时**
UE的隐式detach定时器超时。MME相信UE已经长期在覆盖范围之外，并决定从网络隐式detach掉UE。

**4）【eNB，MME，SGW，PGW，PCRF】UE detached**
MME发起隐式detach过程。这个过程和之前文档中的MME发起的隐式detach过程意义昂。分配给UE的而资源和上下文都被删除了。

# III. EMM Case 11. Initial Attach in Another City

这章描述UE移动到city2，进入LTE覆盖，并选择一个新的小区，执行初始attach，从detach状态转移到connected状态。我们假设UE已经通过MME发起的隐式detach过程从city1的网络中detach掉了，所以在UE和网络MME1中的old GUTI和NAS安全上下文都保持有效。

图4显示了在city2UE初始附着过程的初始附着的类型和功能模块。这个初始附着类型和attach case-5中的类型是一样的。也就是说UE成功从网络detach，UE和old MME中都保留有效的old GUTI和NAS安全上下文，接着UE使用这些参数向new MME做初始attach。UE使用old GUTI作为ID发送attach request消息。这个消息是使用NAS完整性秘钥完保的。这个new MME转发这个消息给old MME，因为old MME可以做完整性检查。

在这篇文档中，假设在old MME上完整性检查通过了。new MME在从old MME获得包括IMSI的上下文后，执行位置更新和EPS会话建立过程。如果在old MME上完整性保护失败，old MME将给new MME发送error消息，接着UE获得IMSI并执行用户鉴权，NAS安全建立，位置更新和EPS会话建立过程。

![这里写图片描述](http://img.blog.csdn.net/20151019105823119)

图5描述了UE在city2的new MME上执行初始附着的过程。

![这里写图片描述](http://img.blog.csdn.net/20151019105922594)

**1）【UE，eNB】建立RRC连接**
一旦进入city2，UE检测到LTE信号，选择一个新的小区，并向eNB请求RRC连接。

**2）【UE，new MME】使用old GUTI向new MME请求初始附着**
UE向MME2发送attach request消息 (Old GUTI, Last Visited TAI, KSIASME, NAS-MAC)，使用oldMME分配的old GUTI作为UE ID。这个罅隙是使用Knasint完整性保护的，通过在无线接口上的RRC connection setup complete消息发送，并在S1接口上的initial ＵＥmessage消息发送。

**3）【new MME】标识old MME**
MME2通过接收到的initial UE message消息检查UE的位置，并从old GUTI中知道它是由old MME1分配的。接着MME2检查在S10接口上和MME1使用old GUTI是否有效，并获得存储在MME1中的UE上下文。

**4）~6）【old MME，new MME】从old MME中获得UE上下文**
4）new MME（MME2）把接收到的attach request消息和old GUTI包裹在identification request消息(Old GUTI, Complete {Attach Request} message from UE)中发送给old MME（MME1）。

5）当接收到new MME的这个消息，oldMME知道这个GUTI是自己分配的，接着通过保留的UE安全上下文对UE发送的attach消息执行完整性检查，完整性检查通过。

6）完整性保护成功后，old MME给new MME通过identification response消息(IMSI, UE-AMBR, UE Security Context (KASME, KSIASME, Unused AVs, NAS Keys, etc))发送无上下文。new MME获得UE上下文。

**7）~10）【old MME，new MME，HSS】在new MME更新位置信息，并在old MME中删除**
7）MME2现在具有了有效的UE上下文，给HSS发送update location request消息(IMSI, MME ID=MME2)来在MME2上注册UE。通过这种方式，MME2给hss通知包含IMSI的UE已经注册上MME2了。hss更新UE新的位置。

8）hss给MME1发送 cancel location request（IMSI）消息来要求MME1删除UE上下文，MME应要求删除UE上下文。

9）MME1通过发送cancel location response（IMSI）消息给hss通知UE上下文已经删除了。

10)HSS通过发送update location answer(IMSI, APN, Subscribed Profile (QCI, ARP, APNAMBR (UL/DL), UE-AMBR (UL/DL))给MME2提供UE的签约配置信息，包括签约QoS信息。所以MME2可以建立EPS会话了。

**11）【new MME】建立EPS会话**
MME2通过使用从MME1接收到的UE上下文和从hss获得签约配置文件建立EPS会话。这个过程和之前文档中叙述的EPS会话建立过程一样。

# IV. EPS Entity Information

在EMM case10移动到另外一个城市过程的前后EPS实体信息的变化如下所示：

 - before
     - 如果UE在connected状态，保持和初始附着过程后存储的信息一样。
     - 如果UE在idle状态，保持和S1释放过程后存储的信息一样。
 - after
     - UE从网络detach。EPS实体中存储的信息和detach过程后存储的信息一样。


在EMM case11在另外一个城市初始附着前后EPS实体信息的变化如下所示：

 - before：UE从网络detach，和detach过程后存储的信息一样。
 - after：UEattach到网络，存储的信息和初始附着过程后的信息一样。

# V. Closing
这篇文档讨论了UE移出LTE覆盖范围之外，UE怎么和新的MME建立初始附着的。这个场景的目的是介绍使用old GUTI初始附着的过程。所以实际移动到另外一个城市的过程将根据不同运营商怎么配置网络和对网络节点的实现。韩国的LTE网络时全国的，所以LTE服务是任何地方可用的。所以LTE覆盖空洞可以在低无线质量的地方找到。实际中，LTE有邻区，LTE or 2/3G。实体网络中，UE执行切换到邻区或者小区重选而不是从网络detach。

# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “LTE EMM Procedure 2. Detach”, January 2014,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6108
[3] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 2. Call Flow of Initial
Attach”, January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6102
[4] 3GPP TS 36.413, “Evolved Universal Terrestrial Radio Access Network (E-UTRAN); S1 Application
Protocol (S1AP)”.
[5] Netmanias Technical Document, “LTE EMM Procedure 5. Periodic TAU”, February 2014,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6193
[6] Netmanias Technical Document, “LTE EMM Procedure 1. Initial Attach – Part 1. Cases of Initial
Attach”, December 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=6098
[7] Netmanias Technical Document, “LTE Identification II: NE and Location Identifiers”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5906
[8] 3GPP TS 23.401, “General Packet Radio Service (GPRS) enhancements for Evolved Universal
Terrestrial Radio Access Network (E-UTRAN) access”.
[9] Netmanias Technical Document, “LTE EMM Procedure 3. S1 Release”, January 2014,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6110
[10] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010
