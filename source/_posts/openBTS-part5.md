---
title: openBTS part5
date: 2018-05-16 10:14:47
tags:
- OpenBTS
---

# 第5章GPRS
移动网络已经在世界的许多地区沦为数据网络。像WhatsApp和Skype这样的OTT服务只需要一个数据管道，参与者可以连接到任何运营商。参与者之间的费用也不取决于地理位置，不同于本地和长途费用。 GPRS的速度太慢，无法支持双向流视频，但可以满足低质量的语音通话。它的速度非常适合电子邮件和OTT短信。传感器和基础设施（如热量和流量传感器或电子和停车计时器）的世界也需要数据连接。这些低带宽机器到机器（M2M）设备，现在被称为物联网（IoT）设备，是GPRS的一种非常常见的用途。 GPRS实际上不是GSM的一部分。它是在GSM已经标准化之后开发的，通常被称为2.5G，而普通的GSM是2G。 OpenBTS可以抽象出这些差异，并在可能的情况下提供统一的配置。

## 启用/禁用
默认情况下，OpenBTS中的GPRS服务被禁用。通过切换GPRS.Enable键现在打开它：
```
OpenBTS> config GPRS.Enable 1
GPRS.Enable changed from "0" to "1"
GPRS.Enable is static; change takes effect on restart
```
重新启动OpenBTS以应用此静态键：
```
$ sudo stop openbts
$ sudo start openbts
```
一旦OpenBTS重新启动，请重新登录其命令行并使用gprs list命令确认OpenBTS已为GPRS设置了几个通道：
```
OpenBTS> gprs list
PDCH ARFCN=166 TN=1 FER=0%
PDCH ARFCN=166 TN=2 FER=0%
```

## 中央服务
GPRS不依赖任何附加组件，但是必须在Linux主机上安装一些配置才能正常工作。在安装过程中，这应该已经从range-configs包中处理好了，但这是如何仔细检查事情是否正常。手机的IP流量通过OpenBTS传输到名为sgsntun的虚拟网络接口中。您现在可以确认OpenBTS已经使用ifconfig创建了它：
```
$ ifconfig sgsntun
sgsntun Link encap:UNSPEC HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
UP POINTOPOINT RUNNING NOARP MULTICAST MTU:1500 Metric:1
RX packets:0 errors:0 dropped:0 overruns:0 frame:0
TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:500
RX bytes:0 (0.0 B) TX bytes:0 (0.0 B)
```
虚拟网络接口还需要应用于iptables Linux防火墙的路由和规则。示例规则位于/etc/OpenBTS/iptables.rules中，如果需要更改网关接口名称，则可以对其进行修改。默认情况下，它们是为eth0编写的。现在手动应用规则：
```
$ sudo iptables-restore < /etc/OpenBTS/iptables.rules
```
为了让系统在每次启动eth0界面时应用这些规则，修改/ etc / network / interfaces以添加下面包含“pre-up”的最后一行：
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
# The loopback network interface
auto lo
iface lo inet loopback
# The primary network interface
auto eth0
iface eth0 inet dhcp
pre-up iptables-restore < /etc/OpenBTS/iptables.rules
```
在隧道设备存在且应用规则，你的Linux主机应该是有序的。

## 连接
尽管您的手机应该执行LUR并在网络恢复后加入网络，但可能还需要其他步骤来确保GPRS服务已被识别并可用。在GPRS中，这不是一个LUR;它被称为GPRS连接。由于几个原因，手机可能无法执行此附件。如果您正在使用其他运营商的SIM卡，则手机GPRS子系统在加入OpenBTS网络时可能会认为自己正在漫游。除非手机中的“使用数据漫游”已打开，否则GPRS子系统不会尝试进行附加。再次检查您的手机的接入点名称（APN）设置。 OpenBTS不关心在名称，用户名和密码字段中放入了什么信息，但手机可能不会发出附加请求，直到输入内容。当涉及APN条目时，Android有一个非常奇怪的错误。在某些版本中，您可以添加新的APN，但除非您输入的移动国家代码（MCC）和移动网络代码（MNC）与SIM卡上定义的那些匹配，否则它不会保存。这个失败是无声的，没有迹象表明新的APN设置不会被使用。它们也不会显示在设备上的APN列表中。但是，如果您将SIM卡替换为与您输入的MCC和MNC相匹配的SIM卡，则会再次出现您的APN信息。尽管如此，有些手机可能仍需要几分钟时间才能连接。他们可能会在放弃并开始新的评估之前向OpenBTS提供旧的网络访问信息。成功连接后，手机的IP地址通过sgsn list命令可见：
```
OpenBTS> sgsn list
GMM Context: imsi=001010000000009 ptmsi=0x47001 tlli=0xc0047001
state=GmmRegisteredNormal age=32 idle=0 MS#1,TLLI=c0047001,7d4373ae
IPs=192.168.99.1
```

## 故障排除
如果您能够连接但尚未收到IP地址，则防火墙设置可能会妨碍您的工作。通常这不是问题，但根据Linux安装和IP网络配置，这是可能的。现在禁用防火墙并重新启动OpenBTS以应用更改：
```
OpenBTS> config GGSN.Firewall.Enable 0
GGSN.Firewall.Enable changed from "1" to "0"
GGSN.Firewall.Enable is static; change takes effect on restart
```
OpenBTS还会尝试检测您的DNS服务器设置并将它们传递到手机。如果您发现手机无法解析域名，请尝试手动设置DNS服务器并重新启动OpenBTS以应用更改：
```
OpenBTS> config GGSN.DNS 8.8.8.8
GGSN.DNS changed from "" to "8.8.8.8"
GGSN.DNS is static; change takes effect on restart
```
网关GPRS支持节点（GGSN）和服务GPRS支持节点（SGSN）通常是GSM中的独立实体网络，但直接嵌入在OpenBTS中。为了更深入地了解这些组件发生了什么，您可以设置一个单独的日志文件，并观察手机与网络上的GPRS服务交互时的内容。将GGSN.Logfile.Name键设置为您希望发送日志的文件路径，然后重新启动OpenBTS以应用更改：
```
OpenBTS> devconfig GGSN.Logfile.Name /tmp/GGSN.log
GGSN.Logfile.Name changed from "" to "/tmp/GGSN.log"
GGSN.Logfile.Name is static; change takes effect on restart
```
现在，当GPRS网络上的活动通过GGSN时，新条目将添加到此日志文件中。使用tail命令和-f标志来监视内容：
```
$ tail -f /tmp/GGSN.log
19:44:04.5: GGSN.MS.IP.Base=192.168.99.1
19:44:04.5: GGSN.MS.IP.MaxCount=254
19:44:04.5: GGSN.MS.IP.Route=192.168.99.0/24
19:44:04.5: GGSN.IP.MaxPacketSize=1520
19:44:04.5: GGSN.IP.ReuseTimeout=180
19:44:04.5: GGSN.Firewall.Enable=0
19:44:04.5: GGSN.IP.TossDuplicatePackets=0
19:44:04.5:GGSN: DNS servers: 8.8.8.8 0.0.0.0
19:44:04.5:ip link set sgsntun up
19:44:04.7:ip route add to 192.168.99.0/24 dev sgsntun
```

## 性能调整
OpenBTS尝试在信令的独立专用控制信道（SDCCH）和媒体的TCH之间智能划分资源。但是，它还没有一种机制来平衡不同类型的TCH使用。
### 语音与GPRS
话音和GPRS业务都使用携带TCH逻辑信道的时隙。如果您的网络将部署为主要服务于GPRS而不是语音，则应调整GPRS.Channels.Min.C0键，该键指定应该用于GPRS的可用TCH时隙的最小数量。默认值是两个。要查看当前可用信道数量，请使用load命令：
```
OpenBTS> load
== GSM ==
SDCCH load/available: 0/4
TCH/F load/available: 0/5
PCH load: active, total: 0, 0
AGCH load: active, pending: 0, 0
== GPRS ==
current PDCHs: 2
utilization: 0%
```
在这里，我们看到有7个可用的TCH信道：GSM语音（GSM：TCH / F）有5个，GPRS数据（GPRS：PDCH）有2个可用。要使GPRS网络最大化，请将GPRS.Channels.Min.C0设置为7，即可用的TCH通道总数，然后重新启动OpenBTS以应用更改：
```
OpenBTS> config GPRS.Channels.Min.C0 7
GPRS.Channels.Min.C0 changed from "2" to "7"
GPRS.Channels.Min.C0 is static; change takes effect on restart
```
重新运行负载将显示所有TCH通道都已分配给GPRS：
```
OpenBTS> load
== GSM ==
SDCCH load/available: 0/4
TCH/F load/available: 0/0
PCH load: active: 0 total: 0
AGCH load: active: 0 () pending: 0
== GPRS ==
current PDCHs: 7
utilization: 0%
```
如果您想手动控制它们，OpenBTS允许您更改所有时间片分配。您可以调整组合1（TCH）与组合7（SDCCH）时隙分配的数量。您还可以调整这些时间段的顺序，以及GPRS时隙应该出现在多ARFCN系统中的位置。通过搜索“Channels”来查看所有键：
```
OpenBTS> config Channels
GPRS.Channels.Min.C0 2 [default]
GPRS.Channels.Min.CN 0 [default]
GSM.Channels.C1sFirst 0 [default]
GSM.Channels.NumC1s auto [default]
GSM.Channels.NumC7s auto [default]
GSM.Channels.SDCCHReserve 0 [default]
```

### 个人手机吞吐量
手机可以同时使用多个时隙来访问GPRS。上行链路和下行链路支持的并行时隙数称为多时隙类。默认情况下，OpenBTS被设置为支持3 + 2多时隙等级：三个下行链路的连续时隙和两个上行链路。这并不意味着一部手机将同时支配所有五个时隙，但您现在可以明白为什么将尽可能多的时隙添加到以GPRS为中心的网络中非常重要。一个更高的多时隙类将向单个手机提供更高速的数据，但随着多个手机试图使用它，网络将变得更加迅速拥塞。您可以使用以下两个键来调整支持的多时隙类：
```
OpenBTS> config Multislot
GPRS.Multislot.Max.Downlink 3 [default]
GPRS.Multislot.Max.Uplink 2 [default]
```

### 覆盖面积与吞吐量
GPRS为传送的数据定义了四种不同的编码方案（CS）。它们被适当命名为CS1，CS2，CS3和CS4。编码方案1的吞吐量最低，但可靠性最高。它分配更多的比特用于消除无线传输过程中引入的错误。另一方面，编码方案4具有最高的吞吐量，但在传输过程中容易出错。这些传输错误限制了每种编码方案的可用覆盖范围。 OpenBTS支持CS1和CS4。您可以选择低吞吐量，但是可以选择可靠的覆盖区域，或者更高的吞吐量，而且鲁棒性较差的纠错会导致覆盖区域更小。默认情况下，OpenBTS已启用，但可以在上行链路和下行链路上单独调整它们：
```
OpenBTS> devconfig Codecs
GPRS.Codecs.Downlink 1,4 [default]
GPRS.Codecs.Uplink 1,4 [default]
```

## 期望
GPRS不是很快，但数据是数据。在无法使用WiFi的地方，相对较大的区域可以使用调制解调器速度的互联网接入，无论是M2M /物联网设备还是联系亲朋好友的人。表5-1列出了每部手机的预期吞吐量。
Table 5-1. Multislot class and coding scheme throughputs

Slots down+up| 1+1| 2+1| 2+2| 3+2
---- | ---- | ---- | ---- | ---- 
CS1| 9.05/9.05 kbps| 18.1/9.05 kbps| 18.1/18.1 kbps| 27.15/18.1 kbps
CS4| 21.4/21.4 kbps| 42.8/21.4 kbps| 42.8/42.8 kbps| 64.2/42.8 kbps

这些都是最佳吞吐量，并且非常依赖于网络拥塞，无论是本地无线电还是互联网上行链路。无可否认，OpenBTS GPRS支持并不完美。 GSM语音和短信几乎都是OpenBTS内部完成的工作，但是GPRS仍然可以进行改进和优化。这些改进大部分与分配接入无线电媒体以及数据包重新安排的算法有关。优化这些将大大提高OpenBTS中的GPRS体验。


