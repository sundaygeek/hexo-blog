---
title: openBTS part2
date: 2018-05-14 19:11:45
tags:
- OpenBTS
---

# 初始测试和配置
现在应该安装软件和硬件。本章将指导您完成一些初步的完整性检查，功能测试和基本配置定制。到本章结束时，您将通过您的私人移动网络在手机间成功交换第一条短信息和语音通话！

## 初始状态
如果服务的其他实例已在运行，则后面的一些手动步骤将会发生冲突并失败。为了确保这个系统上没有别的东西在运行，执行以下命令：
```
$ sudo stop openbts 
$ sudo stop asterisk 
$ sudo stop sipauthserve 
$ sudo stop smqueue
```
现在您可以继续确认链中每个步骤的连通性，然后运行第一个基本试验。

## 确认无线电连接
首先要验证的是收发器应用程序可以与无线电硬件通信。不同的供应商有不同的方法来完成这一点。 

### Ettus Research Radios
所有Ettus硬件均使用Transceiver52M二进制文件，该文件安装在上一章的/OpenBTS中。运行它，如下所示，以查看是否检测到硬件设备：
```
$ cd /OpenBTS 
$ sudo ./transceiver 
[sudo] password for openbts：
linux; GNU C ++版本4.6.3; Boost_104601; UHD_003.006.002-releaseUsing internal clock reference
-- Opening a USRP2/N-Series device...
-- Current recv frame size: 1472 bytes
-- Current send frame size: 1472 bytes
```
上述示例显示尝试成功。收发器可以通过按Ctrl-C来停止。如果您看到类似以下输出的内容，则存在以下问题：
```
$ cd /OpenBTS 
$ sudo ./transceiver 
[sudo] password for openbts：
linux; GNU C++ version 4.6.3; Boost_104601; UHD_003.007.002-release
Using internal clock reference
ALERT 1745:1745 2014-09-05T22:37:00.4 UHDDevice.cpp:528:open: No UHD devices
found with address ''
ALERT 1745:1745 2014-09-05T22:37:00.4 runTransceiver.cpp:160:main: Transceiver
exiting...
```

Ettus提供了一对帮助应用程序来自动检测和检查附加无线电。运行以下命令以列出所有连接的设备。如果未显示，请跳至第18页的“排除USB故障”或第19页的“故障排除以太网”以解决连接问题。这个例子显示了通过以太网连接的N200：
```
$ uhd_find_devices 
linux; GNU C++ version 4.6.3; Boost_104601; UHD_003.007.002-release
UHD Device 0
Device Address:
type: usrp2
addr: 192.168.10.2
name:
serial: XXXXXX
```
另一个有用的应用程序是uhd_usrp_probe，它将检查设备并返回其技术信息和配置。这个应用程序的示例运行如下：
```
$ uhd_usrp_probe
linux; GNU C++ version 4.6.3; Boost_104601; UHD_003.007.002-release
-- Opening a USRP2/N-Series device...
-- Current recv frame size: 1472 bytes
-- Current send frame size: 1472 bytes
_____________________________________________________
/
| Device: USRP2 /N-Series Device
| _____________________________________________________
| /
| | Mboard: N200r4
| | hardware: 2576
| | mac-addr: XX:XX:XX:XX:XX:XX
| | ip-addr: 192.168.10.2
| | subnet: 255.255.255.255
| | gateway: 255.255.255.255
| | gpsdo: none
| | serial: XXXXXX
| | FW Version: 12.3
| | FPGA Version: 10.0
| |
| | Time sources: none, external, _external_, mimo
| | Clock sources: internal, external, mimo
| | Sensors: mimo_locked, ref_locked
| | _____________________________________________________
| | /
| | | RX DSP: 0
| | | Freq range: -50.000 to 50.000 Mhz
| | _____________________________________________________
| | /
| | | RX DSP: 1
| | | Freq range: -50.000 to 50.000 Mhz
| | _____________________________________________________
| | /
| | | RX Dboard: A
| | | ID: SBX (0x0054)
| | | Serial: XXXXXX
| | | _____________________________________________________
| | | /
| | | | RX Frontend: 0
| | | | Name: SBXv3 RX
| | | | Antennas: TX/RX, RX2, CAL
| | | | Sensors: lo_locked
| | | | Freq range: 400.000 to 4400.000 Mhz
| | | | Gain range PGA0: 0.0 to 31.5 step 0.5 dB
| | | | Connection Type: IQ
| | | | Uses LO offset: No
| | | _____________________________________________________
| | | /
| | | | RX Codec: A
| | | | Name: ads62p44
| | | | Gain range digital: 0.0 to 6.0 step 0.5 dB
| | | | Gain range fine: 0.0 to 0.5 step 0.1 dB
| | _____________________________________________________
| | /
| | | TX DSP: 0
| | | Freq range: -250.000 to 250.000 Mhz
| | _____________________________________________________
| | /
| | | TX Dboard: A
| | | ID: SBX (0x0055)
| | | Serial: XXXXXX
| | | _____________________________________________________
| | | /
| | | | TX Frontend: 0
| | | | Name: SBXv3 TX
| | | | Antennas: TX/RX, CAL
| | | | Sensors: lo_locked
| | | | Freq range: 400.000 to 4400.000 Mhz
| | | | Gain range PGA0: 0.0 to 31.5 step 0.5 dB
| | | | Connection Type: QI
| | | | Uses LO offset: No
| | | _____________________________________________________
| | | /
| | | | TX Codec: A
| | | | Name: ad9777
| | | | Gain Elements: None
```

### Range Networks Radios
网络SDR1无线电广播使用TransceiverRAD1二进制文件，该文件安装在上一章的/OpenBTS中。如下运行它，看看是否检测到硬件设备：
```
$ cd /OpenBTS 
$ sudo ./transceiver 1
```
如果程序不立即停止并继续运行，它已成功检测到无线电硬件。收发器可以通过按Ctrl-C停止，如下所示：
```
$ cd /OpenBTS 
$ sudo ./transceiver 1 
^C Received shutdown signal
```

如果程序立即停止，如下面的输出所示，继续进入故障排除USB部分以解决此问题：
```
$ cd /OpenBTS 
$ sudo ./transceiver 1 
$ 
```

### USB故障排除
使用虚拟机设置时，USB连接最常见的问题是所需的USB设备未与正确的虚拟机相关联。使用虚拟机的设置来查找相应的USB设备并将其分配给运行OpenBTS开发环境的虚拟机。对以太网进行故障诊断无论使用虚拟机还是实际服务器，以太网连接的无线电都必须有一个额外的以太网接口。虽然可以更改无线电的IP地址以匹配您的本地网络，但这是不可取的，因为在收发器应用程序和无线电硬件之间通过以太网交换的样本对延迟和丢失非常敏感。他们应该建立专门的连接。在服务器中安装额外的物理以太网接口或在虚拟机中创建额外的虚拟以太网接口。确保该接口与无线电硬件在同一子网上。所有Ettus硬件的默认IP地址是192.168.10.2。使用以下示例为您的额外以太网接口分配适当的地址：
```
$ sudo ifconfig eth1 192.168.10.1/24
```
现在使用简单的ping命令测试连接。按Ctrl-C停止ping测试：
```
$ ping 192.168.10.2
PING 192.168.10.2 (192.168.10.2) 56(84) bytes of data.
64 bytes from 192.168.10.2: icmp_req=1 ttl=64 time=1.037 ms
64 bytes from 192.168.10.2: icmp_req=2 ttl=64 time=1.113 ms
^C
```

## 启动网络
既然您已确认收发器软件可以通信使用无线电硬件，您可以在后台开始运行OpenBTS服务。使用以下命令执行此操作：
```
$ sudo start openbts
```
OpenBTS服务将自动启动收发器软件的实例并连接到无线电硬件。无线电采样然后通过本地用户数据报协议（UDP）套接字在收发器软件和OpenBTS软件之间交换。

### 配置 系统和CLI
OpenBTS的所有配置都是通过操作存储在SQLite3数据库中的密钥来完成的。默认情况下，该数据库存储在/etc/OpenBTS/OpenBTS.db。每个键在OpenBTS中编译的模式中定义，用于验证正在使用的值。这种配置系统所带来的一个优点是，大多数关键值可以在几秒钟内改变并应用于运行系统，而不会中断服务。这些是动态密钥。配置系统中还有一些静态键需要重新启动OpenBTS才能应用更改。

操作配置键最简单的方法是通过OpenBTS命令行界面（CLI）。运行以下shell命令打开它：
```
$ sudo /OpenBTS/OpenBTSCLI
```
现在，您将看到一个OpenBTS提示符。这是包括配置更改在内的命令可以被执行以供OpenBTS处理的地方。从现在起，前缀为$的命令将在Linux命令行上执行。 OpenBTS>前缀的命令用于OpenBTS命令行。打开两个终端窗口可能会很方便，因此不需要不断地输入和退出OpenBTS命令行。

## 改变频段和ARFCN
首先要检查的是无线频段和绝对射频频道号（ARFCN）。无线电波段是四个值中的一个：850,900,1800或1900 MHz，对应于全球可用的四个GSM波段。 ARFCN只是选定频段内的一对频率，将用于无线电信号的传输和接收。每个无线电频段有超过100种不同的ARFCN可以使用。 ARFCN也可以被称为载波（例如，使用多个ARFCN的系统是多载波系统）。选择正确的频段和ARFCN对于监管原因以及避免与当地运营商的干扰很重要。您可以使用OpenBTS config命令检查当前频段和ARFCN设置。这些配置键位于GSM.Radio类别中。要查看名称中带有单词GSM.Radio的所有配置密钥，请输入以下命令：
```
OpenBTS> config GSM.Radio
GSM.Radio.ARFCNs 1 [default]
GSM.Radio.Band 900 [default]
GSM.Radio.C0 51 [default]
GSM.Radio.MaxExpectedDelaySpread 4 [default]
GSM.Radio.PowerManager.MaxAttenDB 10 [default]
GSM.Radio.PowerManager.MinAttenDB 0 [default]
GSM.Radio.RSSITarget -50 [default]
GSM.Radio.SNRTarget 10 [default]
```
GSM.Radio.Band键显示正在使用900 MHz频段，GSM.Radio.C0键表示当前选择该频段的ARFCN＃51。如果您的无线电硬件对特定频率没有限制或优化，则可以继续进行这些设置。消除干扰的简单优化是选择您的国家/地区的其他运营商未使用的频段。一般来说，美洲使用850和1900 MHz系统，而世界其他地区则使用900和1800 MHz。国家列表可以在Wikipedia上找到。另外，如果可能的话，选择较低的频率以改善低功率覆盖。如果您的本地注册管理机构为您指定了特定频段和ARFCN，那么您必须使用它。要更改您的GSM频段，您必须再次使用OpenBTS config命令。这一次，将所需的乐队添加到该命令的末尾。以下示例将频段更改为850 MHz：
```
OOpenBTS> config GSM.Radio.Band 850
GSM.Radio.Band changed from "900" to "850"
WARNING: GSM.Radio.C0 (51) falls outside the valid range of ARFCNs 128-251 for
GSM.Radio.Band (850)
GSM.Radio.Band is static; change takes effect on restart
```
该命令确认该Band已更改，但传递了另外两条信息。首先，关于ARFCN对850MHz频段无效的警告。 850 MHz的有效范围是128-251。其次，通知您GSM.Radio.Band参数是静态的，不能在运行时应用; OpenBTS必须重新启动。要修复第一条警告，请再次使用config命令为850 MHz频带设置有效的ARFCN：
```
OpenBTS> config GSM.Radio.C0 166
GSM.Radio.C0 changed from "51" to "166"
GSM.Radio.C0 is static; change takes effect on restart
```
该命令确认ARFCN已更改，并再次警告此参数为静态。您现在可以重新启动OpenBTS以应用更改：
```
$ sudo stop openbts
openbts stop/waiting
$ sudo start openbts
openbts start/running, process 6075
```
该服务将需要几秒钟时间才能开始备份，并且您可以再次自由使用OpenBTS CLI 。

