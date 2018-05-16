---
title: openBTS part4
date: 2018-05-16 09:30:26
tags:
- OpenBTS
---

# 第4章从单个到多个节点
具有单塔的移动网络比具有零塔的移动网络更有用。然而，无论单塔如何调整，都无法有效覆盖特定区域。
由于OpenBTS使用相同的软件堆栈来实现大范围和小范围的覆盖区域，甚至可以在Raspberry Pi上运行，因此试图释放您对经典“细胞塔”图像的想法。塔可以很容易地放入鞋盒或与您的WiFi接入点融合。
本章介绍如何将网络扩展到多个物理站点，但仍然保持单个逻辑网络。这个逻辑网络将支持任何商业网络的移动性和切换。


## 移动性，切换和漫游
这些术语之间存在一些混淆。虽然它们确实意味着非常具体的事情，但即使是该领域的专家也会将它们放在讨论中，并希望对方从背景中理解正确的含义。为了澄清这个歧义，我们将简要概述每个术语。

### 移动性
移动性是手机在运营商网络中的不同物理基站上接收服务的能力。当手机在三维空间中移动时，它从相邻基站接收到的信号质量将会波动。当手机检测到来自邻近基站的显着更好的信号时，它发送LUR以注册或“驻扎”到新的基站。为了区分，在同一基站的定期LUR刷新现有注册，但新基站的LUR重新刷新，将注册切换到新塔。除非两个基站具有不同的位置区域码（LAC），否则不会执行LUR。在传统的GSM网络中，给定地理区域内的所有基站都具有相同的LAC。然而，OpenBTS目前要求所有基站都有独特的LAC，因此在基站之间移动时将执行LUR。这触发更新的SIP REGISTER消息，并因此触发包含新基站的IP的更新的订户注册表项。
移动性是网络的一个功能，但手机决定重新播放。而且，只有当手机没有处于诸如语音呼叫或交换SMS之类的活动交易时也是如此。移动性也可以被称为“空闲模式重新征卡”。

### 切换
切换是在两个基站之间的过渡期间存活的语音呼叫的能力。移动性是切换的先决条件，但与移动性不同，网络决定并执行切换。手机别无选择，只能服从网络发送它的切换命令。
基站控制器（BSC）控制传统GSM网络中的切换。 OpenBTS通过使用新的对等协议消除了对BSC的需求。通过此协议交换关于相邻频率，身份和活动事务的信息，从而简化部署体系结构。
几个因素决定执行切换的决定。首先，来自当前服务该手机的基站的下行链路信号必须足够弱。而且，来自最强的相邻基站的信号也必须超过服务基站电平一个设定的阈值。最后，最强的相邻基站最近不会因拥塞而拒绝任何切换。如果满足这些条件，则服务基站将发起切换到最强的相邻基站。

### 漫游
漫游是跨运营商的移动性。它需要这些运营商和通用接口技术之间的管理协议，通常是7号信令系统（SS7）网络上的GSM-Mobile应用部分（MAP）信令。在你自己的网络中漫游是不可能的。

## 拓扑
到目前为止，您一直在构建一个“网箱在线”拓扑结构，每个单个组件都位于单个服务器上，即单个逻辑实体。在多节点网络中，现在有两个逻辑实体：中央服务和“塔”。到目前为止，您熟悉的组件必须进行重新安排以支持这一点。中央服务将需要安装SIPAuthServe，SMQueue和Asterisk。为了简单起见，SIPAuthServe，SMQueue和Asterisk在本章剩余部分将被称为中央服务。塔将只运行OpenBTS。将有一个单一的中央服务安装和多塔安装，如图4-1所示。
Snipaste_2018-05-16_10-01-09.png

Towers通过IP回传到中心服务，允许多个覆盖区域共享相同的用户数据库和配置。在以下说明中，中央服务将位于192.168.158.100，而塔将从192.168.158.201开始。

## 中央服务设置
而不是从现有的盒内网络配置中提取中央服务，只需将OpenBTS实例和无线电硬件移动到新家即可。这避免了必须移植SMQueue，SIPAuthServe和Asterisk的订户数据和配置。
### 删除OpenBTS
为了确保OpenBTS不会在启动时启动，并且因为缺少收音机而不断失败，请立即将其卸载：
```
$ sudo apt-get remove openbts
```

### 配置日志记录
独立的每座塔楼以及中央服务部门都将生成记录信息。您需要将中央服务设置为接受来自所有塔的记录信息，以便通过网络捕获所有活动。
这一步不是建立功能多节点网络所必需的，但在调试时非常有用。使用单独的日志追踪多个塔中的错误并不是很有趣。
用你最喜欢的编辑器编辑/etc/rsyslog.conf并在“提供UDP系统日志接收”之后直接取消注释这两行：
```
# provides UDP syslog reception
$ModLoad imudp
$UDPServerRun 514
```

这两行意味着rsyslog将接受端口514上的UDP日志通信。要应用这些设置，需要重新启动该服务：
```
$ sudo service rsyslog restart
```

### Asterisk，SMQueue和SIPAuthServe
这些组件中无需重新配置。他们不关心OpenBTS实例是本地通信还是通过IP网络进行通信。

## 塔设置
跳回到第7页的“操作系统和开发环境设置”，并按照新的虚拟机或新服务器来启动并运行新塔的操作系统和组件。确保选择塔的唯一主机名，以便在集中日志中区分它。此外，只需安装OpenBTS，在这些说明的末尾节省一些时间。
这些说明假定您的塔式计算机和中央服务计算机位于同一个网络子网上。一旦登录到新的塔式安装，确保您可以通过中央服务（按Ctrl-C停止ping命令）：
```
$ ping 192.168.158.100
PING 192.168.158.100 (192.168.158.100) 56(84) bytes of data.
64 bytes from 192.168.158.100: icmp_req=1 ttl=128 time=4.28 ms
64 bytes from 192.168.158.100: icmp_req=2 ttl=128 time=4.34 ms
64 bytes from 192.168.158.100: icmp_req=3 ttl=128 time=4.48 ms
64 bytes from 192.168.158.100: icmp_req=4 ttl=128 time=4.48 ms
^C
--- 192.168.158.100 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 4.285/4.399/4.484/0.086 ms
```

### 配置SIP代理
OpenBTS使用SIP连接到所有其他组件。由于这些组件位于另一台主机上，因此需要对SIP.Proxy键进行相应的调整。此外，现在OpenBTS正在与除localhost之外的IP地址上的服务通话，它需要知道将自己的请求发送到哪个地址。 SIP.Local.IP键允许您设置其他服务在联系OpenBTS或回复其请求时应使用的IP地址。现在使用塔的IP地址进行设置：
```
OpenBTS> config SIP.Local.IP 192.168.158.201
SIP.Local.IP changed from "127.0.0.1" to "192.168.158.201"
SIP.Local.IP is static; change takes effect on restart
```
重新启动OpenBTS以应用此静态键：
```
$ sudo stop openbts
$ sudo start openbts
```
现在，每个SIP.Proxy键都可以更新为指向中央服务，因此注册，语音和短信流量将发送到那里，而不是本地主机：
```
OpenBTS> config SIP.Proxy.Registration 192.168.158.100:5064
SIP.Proxy.Registration changed from "127.0.0.1:5064" to "192.168.158.100:5064"
OpenBTS> config SIP.Proxy.SMS 192.168.158.100:5063
SIP.Proxy.SMS changed from "127.0.0.1:5063" to "192.168.158.100:5063"
OpenBTS> config SIP.Proxy.Speech 192.168.158.100:5060
SIP.Proxy.Speech changed from "127.0.0.1:5060" to "192.168.158.100:5060"
```
确保包含端口信息。每个组件都运行在不同的端口上，并将请求路由到不正确的端口会破坏功能。

强制手机通过这种新拓扑进行注册以测试事情。语音和短信也应该起作用。在继续之前验证这一点。

### 配置日志记录
记录每个塔产生的信息可以发送到中央服务。中央服务上的日志记录守护程序已更改为接受此通信;现在是时候告诉塔节点发送它。使用您最喜欢的编辑器编辑/etc/rsyslog.d/OpenBTS.conf，以便它在现有local7条目正下方包含一个新行：
```
local7.* /var/log/OpenBTS.log
local7.* @the-central-services-ip
```
此行意味着除了将信息记录到磁盘上的OpenBTS.log文件之外，还会将相同的消息发送到中央服务以便在那里记录。要应用这些设置，需要重新启动服务：
```
$ sudo service rsyslog restart
```
### 拓扑重做
到目前为止，您的网络在功能上是相同的。仍然有一个覆盖区域由一个逻辑塔和中央服务创建 - 它们只是分成两个独立的物理机器。下一步需要向网络添加更多的邻近塔，并将其配置为单个RAN。

## 添加相邻的塔
塔架设置部分现在可以重复用于您想要添加的任意多个物理塔架。一旦它们被配置为至少起到独立塔的作用，它们必须被配置为在网络内作为适当的邻近塔。此外，所有现有的塔必须被告知这个新的塔。
到目前为止，假设您的网络中只有一个塔 - 您刚刚从网络集中式布局移植的塔。现在看看它的身份参数：
```
OpenBTS> config Identity
GSM.Identity.BSIC.BCC 2 [default]
GSM.Identity.BSIC.NCC 0 [default]
GSM.Identity.CI 10 [default]
GSM.Identity.LAC 1000 [default]
GSM.Identity.MCC 001 [default]
GSM.Identity.MNC 01 [default]
GSM.Identity.ShortName GroundControl
```
其中一些参数必须是唯一的，而其他参数必须在所有邻居中相同。表4-1是一个方便的总结。配置模式也将这些信息嵌入到作用域中。范围字段会告诉您键必须是唯一的还是相邻的塔之间或网络中的所有塔之间。
Table 4-1. Configuration scope

Identical | Unique
---- | ----
GSM.Identity.MCC | GSM.Identity.LAC
GSM.Identity.MNC | GSM.Identity.CI
GSM.Identity.BSIC.NCC | GSM.Identity.BSIC.BCC
GSM.CellSelection.NCCsPermitted | GSM.Radio.C0
GSM.Identity.ShortName | 

一些背景信息将被呈现，因此每个参数都可以被理解，以及设置你的下一个塔的分步指南。

### 必须完全一致
GSM.Identity.MCC和GSM.Identity.MNC键是任何网络的最高级别标识，指示该网络在哪个国家以及哪个运营商正在运行它。监管机构分配这两个ID。测试网络总是分别使用001和01。如果这不是一个测试网络，那么您可能已经为许可证付了钱，并且非常了解适当的价值。
GSM.Identity.BSIC.NCC键是您的网络颜色代码（NCC），这是手机用来快速确定他们是否可以访问网络的信息。特定区域内的所有运营商必须具有唯一的颜色代码。
与最后一个键相关的是GSM.CellSelection.NCCsPermitted。在与合作伙伴网络合作时使用此键来表达以下内容：“不仅我的NCC正常，而且允许其他NCC。”NCC许可键是一个按位标志，而不是一个整数值。无论如何，所有的塔都应该具有相同的NCCsPermitted设置。
GSM.Identity.ShortName键在所有塔中应该是相同的，但它不是必需的。部署多节点网络时，一种方便的调试技术是为每个塔设置唯一的短名称。如果您的手机可靠地显示短名称，您现在可以立即知道手机在移动时驻留的塔楼。
一步步
本节的逐步说明非常基本：确保所有这些键与网络中现有的塔相同。

### 必须是唯一的
第一个键GSM.Radio.C0必须是唯一的，因为它是ARFCN，因此在该塔上使用RF。如果物理上相邻的塔使用相同的频率，则会对重叠信号区域中的任何参与者造成干扰并拒绝服务。
C0 ARFCN不仅必须是唯一的，而且它们也不能在数字上相邻。由于信号间隔仅为200 KHz，信号带宽为270 KHz，相邻的ARFCN重叠。部署物理上相邻的塔时，至少使用其他每个ARFCN（例如，塔1使用ARFCN 151，塔2使用ARFCN 153）。
GSM.Identity.BSIC.BCC对于物理上相邻的塔也必须是唯一的。它发信号给基站色码（BCC），一个3位字段允许7个唯一值。
您获得许可的C0 ARFCN和BCC数量非常有限，如果您打算购买大量的色彩地图，可能会有助于将值分配给塔。彩色地图显示所有塔的地理位置并为每个塔分配一种颜色。每种颜色对应于ARFCN编号和BCC对，如图4-2所示。如果您的计划显示没有两个相邻的塔楼具有相同的“颜色”，则移动性和交接不会因与ARFCN或BCC冲突而受到阻碍。
Snipaste_2018-05-16_10-04-42.png


GSM.Identity.LAC在您的网络中的所有信号塔中必须是唯一的。当手机穿过塔台边界时，它意识到位置区域代码正在改变，并且应该执行另一个LUR。该LUR被转换为SIP REGISTER，并且SIPAuthServe可以更新该手机可达的塔的IP地址。 LAC独特的要求特定于OpenBTS。传统网络的LAC对应于包含多个塔的更大的地理区域.GSM.Identity.CI是单元ID（CI），并且在网络中的所有塔中必须是唯一的。
#### 一步步
为新塔分配颜色并使用相应的C0值和BCC：
```
OpenBTS> config GSM.Radio.C0 168
GSM.Radio.C0 changed from "151" to "168"
GSM.Radio.C0 is static; change takes effect on restart
OpenBTS> config GSM.Identity.BSIC.BCC 3
GSM.Identity.BSIC.BCC changed from "2" to "3"
```
由于有很多LAC和CI值，只需使用每个塔的下一个：
```
OpenBTS> config GSM.Identity.LAC 1001
GSM.Identity.LAC changed from "1000" to "1001"
OpenBTS> config GSM.Identity.CI 11
GSM.Identity.CI changed from "10" to "11"
```
重新启动OpenBTS以应用更改：
```
$ sudo stop openbts
$ sudo start openbts
```

### 邻居列表和命令
你的塔现在被配置为不会在物理上通过其传输频率或逻辑上在他们的ID编号方案中彼此踩踏。他们仍然必须在配置中被告知彼此。这是使用GSM.Neighbors键完成的。每个塔必须将其GSM.Neighbors键设置为与所有物理相邻塔对应的空间分离IP地址列表，但不包括其本身。
在您的新塔楼（192.168.158.202）上，输入以下内容以告知您的原塔楼（192.168.158.201）：
```
OpenBTS> config GSM.Neighbors 192.168.158.201
GSM.Neighbors changed from "" to "192.168.158.201"
```
对原来的塔做同样的事情，以便了解新塔的情况：
```
OpenBTS> config GSM.Neighbors 192.168.158.202
GSM.Neighbors changed from "" to "192.168.158.202"
```
仍然在你的原始塔，192.168.158.201，检查邻居命令：
```
OpenBTS> neighbors
host C0 BSIC FreqIndex Noise ARFCNs TCH-Avail Updated Holdoff
------------------ --- ---- --------- ----- ------ --------- ------- -------
192.168.158.202:16001 168 2 0 68 1 7 16 0
```
OpenBTS对等协议自动查询有关GSM.Neighbors中定义的相邻基站的信息。 neighbors命令提供对这些信息的访问。在这里您可以看到C0 ARFCN，当前噪声级别，总ARFCN数量和可用业务信道（TCH）。

### 启用了邻居的命令
既然您有一对功能相近的人，chans命令也会在通话期间提供更多信息。该信息与手机报告的最强邻居塔的当前接收等级有关。要查看它，请使用带有-l标志的chans命令：
```
OpenBTS> chans -l
CN TN chan transaction LAPDm recyc Signal RSSI RSSP SNR FER BER TA type id state dB dB dB pct pct sym
0 1 TCH/F T100 LinkEstablished false 26 -50 -24 38.7 0.00 0 0.9
TXPWR TA_DL RXLEV_DL BER_DL Time IMSI Neighbor Handover
dBm sym dBm pct M:S ARFCN(dBm) C0:BSIC
7 0 -93 2.26 0:25 2140... 168 (-76)
```

##覆盖重叠调整
每个塔单独调谐，最棘手的部分是相互调整它们。再看一下图4-1。塔1和塔2之间的重叠区域是至关重要的。该区域必须足够宽，以便穿过它的手机可以可靠地与两个塔接触，只要它执行切换。必须考虑该区域的地形以及潜在的手机速度。重叠不能太过于浪费资源。在可靠的重叠和优化的整体覆盖范围之间找到平衡是关键。再次，整个企业都可以建立在这种专业知识基础之上，深入的研究不在“入门”的范围之内。
手机移动只有一个键来配置：GSM.CellSelection.CELLRESELECT-HYSTERESIS。这个关键指示手机在重新播放之前应该有多好的潜在邻近塔的信号。

OpenBTS可以更直接地控制切换。有几个键可用于调整切换行为：
```
OpenBTS> config Handover
GSM.Handover.FailureHoldoff 20 [default]
GSM.Handover.Margin 15 [default]
GSM.Handover.Ny1 50 [default]
GSM.Timer.Handover.Holdoff 10 [default]
```
GSM.Handover.Margin键与GSM.CellSelection.CELL-RESELECTHYSTERESIS类似，只是它用于切换操作。在这两种设置下，重要的是要记住，如果它们设置得太高（例如，在切换之前塔必须强得多），则存在手机不能及时跳过的风险。如果它们设置得太低（例如，在切换之前塔必须稍微强一些），当手机快速地来回跳动时，网络可能被位于两个信号大致相等的区域中的手机流量峰值所击中。
如果您想尝试OpenBTS切换算法中的权重，那么使用devconfig还可以使用更多参数：
```
OpenBTS> devconfig Handover
GSM.Handover.FailureHoldoff 20 [default]
GSM.Handover.History.Max 32 [default]
GSM.Handover.Margin 15 [default]
GSM.Handover.Ny1 50 [default]
GSM.Handover.RXLEV_DL.History 6 [default]
GSM.Handover.RXLEV_DL.Margin 10 [default]
GSM.Handover.RXLEV_DL.PenaltyTime 20 [default]
GSM.Handover.RXLEV_DL.Target 60 [default]
GSM.Timer.Handover.Holdoff 10 [default]
```

