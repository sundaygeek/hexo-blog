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
使用虚拟机设置时，USB连接最常见的问题是所需的USB设备未与正确的虚拟机相关联。使用虚拟机的设置来查找相应的USB设备并将其分配给运行OpenBTS开发环境的虚拟机。

### 以太网进行故障诊断
无论使用虚拟机还是实际服务器，以太网连接的无线电都必须有一个额外的以太网接口。虽然可以更改无线电的IP地址以匹配您的本地网络，但这是不可取的，因为在收发器应用程序和无线电硬件之间通过以太网交换的样本对延迟和丢失非常敏感。他们应该建立专门的连接。在服务器中安装额外的物理以太网接口或在虚拟机中创建额外的虚拟以太网接口。确保该接口与无线电硬件在同一子网上。所有Ettus硬件的默认IP地址是192.168.10.2。使用以下示例为您的额外以太网接口分配适当的地址：
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

### 改变频段和ARFCN
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

### Range Networks Radio 校准
   Range Networks SDR1硬件可以配备滤波器，针对特定的GSM频段优化无线电。 每个单元也被校准到一个特定的频带，并且如果OpenBTS设置不符合这个校准，可能会表现不佳。 要查看收音机的出厂校准，请使用trxfactory命令：
```
OpenBTS> trxfactory
Factory Information
SDR Serial Number = XXXX
RF Serial Number = XXX
GSM.Radio.Band = 850
GSM.Radio.RxGain = 52
TRX.TxAttenOffset = 1
TRX.RadioFrequencyOffset = 116
```
要确定是否需要调整，请使用audit命令。 在对安装进行故障排除时，audit命令是您不变的伴侣。 它会报告非默认值，无效值和冲突值，如果您使用的是范围网络硬件，则会列出出厂校准和当前运行配置之间不匹配的部分：
```
OpenBTS> audit
+---------------------------------------------------------------------+
| WARNING : Factory Radio Calibration [key current-value (factory)] |
| To use the factory value again, execute: rmconfig key |
+---------------------------------------------------------------------+
TRX.RadioFrequencyOffset "132" ("116")
```
在本例中，TRX.RadioFrequencyOffset参数与工厂校准不匹配。 如消息所示，可以使用rmconfig命令再次使用出厂值：
```
OpenBTS> rmconfig TRX.RadioFrequencyOffset
TRX.RadioFrequencyOffset set back to its default value
TRX.RadioFrequencyOffset is static; change takes effect on restart
```
这是另一个需要重新启动OpenBTS的静态参数，如上所述。 大多数配置参数不是静态的，因此重新启动OpenBTS将成为例外而不是规则。 对于报告为不匹配的任何其他校准参数重复此过程。 

### Ettus Research无线电校准
Range Networks和Ettus Research无线电之间的一个主要区别在于GSM.Radio.RxGain的适当值。 Range Networks对这个参数使用了一个更高的值，如果它没有调整，Ettus Research设备将无法正常工作。 接收到的信号将过载驱动解调器。 对于初学者，请将GSM.Radio.RxGain设置为10：
```
OpenBTS> devconfig GSM.Radio.RxGain 10
GSM.Radio.RxGain changed from "52" to "10"
GSM.Radio.RxGain is static; change takes effect on restart
```
还有一个专用的命令，可以让你设置这个参数而不用重新启动OpenBTS。 如果您需要进行微调以避免每次重新启动，请使用rxgain。

## 搜索网络
既然Radio已经过校准并且设置已确认，您将使用手机搜索新创建的网络。 每部手机的菜单都不同，但该项通常与“运营商选择”或“网络选择”类似。图2-1中详细介绍了在Android上手动选择不同运营商的流程。
Snipaste_2018-05-15_09-10-20.png

1. 从Android菜单系统启动“设置”应用程序。
2. 选择“更多”。
3. 选择“移动网络”。
4. 选择“网络运营商”。这可能会也可能不会启动搜索。 如果没有，请选择“搜索网络”。
5. 搜索完成后，将显示可用运营商网络列表。

图2-2详细说明了在iOS 7上手动选择不同运营商的流程。
Snipaste_2018-05-15_09-11-23.png

1.在主屏幕中，打开“设置”应用程序。
2.选择“运营商”。
3.在“网络选择”屏幕上，禁用自动载波选择。
4.手机现在将搜索可用的运营商网络。
5.搜索完成后，将显示可用运营商网络列表。

我们在这里看到了可选运营商列表中的测试网络。 根据使用的手机型号，固件和SIM卡，网络ID将显示为“00101”，“001-01”，“测试PLMN 1-1”或GSM“OpenBTS”的短名称。如果您的测试网络 没有被检测到，通过重新选择菜单项，在打开和关闭之间切换飞行模式，或者关闭手机电源，再次强制搜索。 如果仍然无法使用，请再次确认手机支持您在上面配置的GSM频段，并且基带已解锁（即不受合同限制仅使用特定载波）。 本部分让您验证下行链路是否正常工作; 以下部分将指导您验证上行链路。 然后，您将继续输入手机的身份参数并通过第一次连接（第27页）连接至网络。

## 测试无线电频率环境因素
如果您从未有过涉及射频或模拟信号的项目，那么您可能会对可能出现问题的数量感到惊讶。最终，您可能会惊讶于RF通信可以工作！在OpenBTS社区中的射频专家有时被视为黑魔法从业者...但我离题了。在GSM网络中，使用两个独立的射频，因此基站和手机可以在两个方向上同时通信。换句话说，GSM使用频分多址建立全双工通信。选择ARFCN（请参阅第20页的“更改频带和ARFCN”）决定使用哪一对频率。从基站到手机的路径称为下行链路，从手机返回到基站的路径称为上行链路。在上一节中，您已经能够成功搜索并找到新的网络信标。这表明网络的下行链路到达了您的手机，并且信号足够干净以解调和解释所显示的网络短名称和/或身份号码所证明的信息。在建立新网络时需要注意的一点是，无线电干扰或来自上行链路上其他来源的“噪音”。如果上行链路噪声太大，则来自手机的信号不能可靠地解调成可用信息。 OpenBTS将通过使用noise命令显示当前级别：
```
OpenBTS> noise
noise RSSI is -68 dB wrt full scale
MS RSSI target is -50 dB wrt full scale
INFO: the current noise level is acceptable.
```

在本例中，检测到的环境噪声接收信号强度指示（RSSI）为-68 dB（较低的数字更好，意味着噪声较少）以及配置的目标RSSI级别手机是-50分贝。这意味着基站最多可以从手机获得比环境噪声多18dB的能量 - 这是一个非常好的余量，这意味着由于噪声导致的上行链路接收问题不应该成为问题。这两个数字之间的小利润将产生不同的信息。例如，具有10dB或更小的裕量将报告：
```
WARNING: the current noise level is approaching the MS RSSI target, uplink connectivity will be extremely limited.
警告：当前的噪声级别接近MS RSSI目标，上行链路连接将非常有限。
```
并且零或小于零的边际将报告：
```
WARNING: the current noise level exceeds the MS RSSI target, uplink connectivity will be impossible.
警告：当前噪声级别超过MS RSSI目标，上行链路连接将不可能。
```
如果报告了这些WARNING消息中的任一个，则需要采取措施来减少上行链路噪声和/或增加手持机发送功率。将来，如果您的手机可以看到基站但无法再连接，应该首先检查噪声。你的配置仍然是100％正确和功能性的，但是无线电环境可能已经改变，阻止了通信。

### 降低噪声
如果您的基站无线电设置不包含频率双工器，上行链路上的头号噪声源实际上可能是下行链路信号。如果没有适当的双工滤波，下行链路信号通常是物理上和频率上最接近上行链路的能量源。有关双工器的更多信息，请参阅第46页的“更强，更清洁的信号”。即使没有双工器，也可以通过多种方法降低上行链路噪声。

#### 天线对齐
一种快速双工器可以简单地对齐天线，使它们不容易相互馈入。如果您使用橡胶鸭式天线，请将它们倾斜成90度角。这些天线的辐射方向图将如图2-3所示垂直。
Snipaste_2018-05-15_16-39-38.png

如果天线彼此平行，则信号可以有效地从发射机流向接收天线，但是当天线形成90度角时，信号在与接收天线不同的平面上发射。通过在调整前后运行噪音命令来观察更改。这种简单的调整可以将噪音降低多达10 dB。

#### 下行链路传输功率
上面的对齐步骤减少了从发射天线到接收天线的能量流。收到的能量可能仍然太高，无法使上行链路可用。降低下行链路传输功率将进一步清除上行链路。在实验室环境中，通过降低下行功率而损失的覆盖区域并不重要。清洁信号优于强信号。不带任何参数运行电源命令以查看当前级别。以衰减分贝报告功率：
```
OpenBTS> power
current downlink power 0 dB wrt full scale
```
为了降低下行链路发射功率，例如减少20dB，输入以下内容：
```
OpenBTS> power 20
current downlink power -20 dB wrt full scale
```
下行链路现在以20dB的功率发射。使用noise命令观察改进。

### 提升手机功率
通过调整GSM.Radio.RSSITargetand和GSM.Radio.SNRTarget键，还可以告诉手机PowerHandsets使用更多的电源。在大多数情况下，这些键的默认值应该足够。但是，如果您遇到接收功率的大幅波动（例如，在地下矿井的角落四处走动），可能有必要增加这些值以提供更大的缓冲区以允许功率差异。提高所有手机的功耗将更快地耗尽电池电量，但上行链路信号更加可靠。总是存在权衡。如果您的噪声级别仍然过高，请返回第19页上的“启动网络”以将您的ARFCN更改为噪声较小的一个。

## 第一连接
您现在已验证下行链路和上行链路。为了确保所有的设置都已经被应用，请发出以下命令来重新启动OpenBTS：
```
$ sudo stop openbts 
$ sudo start openbts
```
在实际连接到测试网络之前的唯一一步就是查找并隐藏手机的身份参数，以便将其接收到网络上。

### 查找IMSI
您将要搜索的主要身份参数是国际移动用户身份（IMSI）。这是存储在SIM卡中的14-15位数字，与网络上的手机用户名类似。手机通常不会泄露其SIM卡的IMSI。它有时可能位于菜单中或通过现场测试模式，但是这种确定SIM的IMSI的方法很麻烦。幸运的是，还有其他方法。 OpenBTS也知道它与之交互过的IMSI，因为你掌控着网络端，所以你也可以访问这些信息。为了强制手机和测试网络之间的交互，你需要执行更新请求（LUR）操作网络，类似于注册。这不比从运营商选择列表中选择网络更复杂。在尝试任何LUR之前，您需要启动负责处理这些请求的SIPAuthServe守护进程：
```
$ sudo start sipauthserve
sipauthserve start / running，process 7017
```
现在，再次按照第23页上的“搜索网络”中的步骤，调出载波选择列表并选择您的测试网络。在很短的时间之后，手机应报告注册失败。它也可能会从您的测试网络收到一条短信，指示注册失败。此消息自动包含IM-SI，因此您可以跳到第29页上的“添加订户”。但是，此功能在所有硬件上都不起作用，因此如果您未收到SMS，则继续操作. OpenBTS会按顺序记住这些LUR交互执行一些称为SIM /临时移动订户身份（TMSI）交换的功能。 IMSI / TMSI交换用于TMSI的用户可识别的IMSI，并用于增加网络上的用户隐私。交易所默认是禁用的（修改Control.LUR.SendTMSIs toenable）;但是，信息仍然存在，可以使用tmsis命令进行检查。现在，请使用它查看与手持设备最近的LUR交互：
```
OpenBTS> tmsis
IMSI TMSI IMEI AUTH CREATED ACCESSED TMSI_ASSIGNED
214057715229963 - 012546629231850 0 78s 78s 0
001010000000002 - 351771054186520 1 80h 95s 0
001010000000003 - 351771053005400 1 80h 108s 0
```
条目按时间排序，最上面的条目对应于最近的交互操作。您的手机应该是此列表中的首要条目 - 最近与AUTH设置为0的交互，因为LUR由于手机不是知识订阅者而失败。本例中的其他条目是成功执行LUR的附加测试手机，如AUTH列设置为1所示。

### 查找IMEI
在繁忙的环境中，可能很难确定哪个手机硬件与此列表上的哪个条目相对应。要将IMSI与特定硬件匹配，可以使用国际移动设备标识符（IMEI）。这是手机物理无线电硬件的唯一标识符，类似于以太网接口上的MAC地址。手机的IMSI通常印在其电池盖下或非常靠近SIM本身的地方。在许多手机上，IMEI也可以通过在键盘上拨打以下内容来访问：\*＃06＃.IMEI值通常仅用于在生产环境中报告和检测被盗硬件。这里可以作为确定哪个SIM卡在哪个手机中的便捷方式。 IMEI的最后一位可能与OpenBTS显示的不匹配。它是一个校验位，在OpenBTS中显示为零。

### 添加订户
您现在应该拥有所有必要的信息以在您的测试网络上创建新的订户帐户。一些字段仍然需要，但可以自由选择：名称和移动台国际用户目录号码（MSISDN）。名称字段仅仅是该用户的友好名称，因此您可以记住与哪个手机或与哪个人相关联。 MSISDN字段并不比订户的电话号码复杂。由于您没有连接到公共电话网络，因此您可以选择任何号码。您需要添加订户的程序是nmcli.py。它是No-deManager API的简单客户端（更多内容参见第7章），并允许您通过JSON格式的命令更改配置参数，添加订阅者，监控活动等。 nmcli.py已经存在于你的开发目录中 - 现在移动它来访问它：
```
$ cd dev /NodeManager
```
有两种方法可以使用nmcli.py添加订阅者。第一个创建将使用缓存身份验证的订户：
```
$ ./nmcli.py sipauthserve subscribers create name imsi msisdn
```
第二个创建将使用完全身份验证的订户：
```
$ ./nmcli.py sipauthserve subscribers create name imsi msisdn ki
```
如果IMSI是“用户名”，手机的“密码”是第5页的“使用备用SIM卡”中讨论的Ki字段。如果您使用来自其他提供商的备用SIM卡，则无法使用Ki，并应使用第一个调用样式。如果你正在使用自己烧过的SIM，Ki会被你知道。使用nmcli.py的第二个调用样式将它包含在新订户中。在这个例子中，在iPhone 4中使用备用SIM卡，并在南达科他州（区号605）为其指定一个假号码：IMSI字段由数字IMSI组成，前缀为字符串“IMSI”。
```
$ ./nmcli.py sipauthserve subscribers create "iPhone 4" IMSI214057715229963 6055551234
raw request: {"command":"subscribers","action":"create","fields":
{"name":"iPhone 4","imsi":"IMSI214057715229963","msisdn":"6055551234","ki":""}}
raw response: {
"code" : 200,
"data" : "both ok"
}
```
使用您自己的信息执行相同的命令，将第一个用户添加到您的测试网络。

### 连接
现在，当您在连接菜单中选择测试网络时，LUR应该成功。 这可以通过OpenBTS中的tmsis命令来确认。 “AUTH”列现在在对应于您的IMSI的条目中具有“1”：
```
OpenBTS> tmsis
IMSI TMSI IMEI AUTH CREATED ACCESSED TMSI_ASSIGNED
214057715229963 - 012546629231850 1 11m 56s 0
001010000000002 - 351771054186520 1 80h 8m 0
001010000000003 - 351771053005400 1 80h 9m 0
```
恭喜，您已成功注册到您自己的私人移动网络！ 在继续之前，请随时注册您想要使用的任何其他手机。

## 测试短信
现在手机可以访问您的网络，您可以执行一些更有趣的测试。 首先是对网络SMS功能的快速测试。 负责接收，路由和安排SMS消息传送的组件是SMQueue。 必须在测试这些功能之前启动它; 执行以下命令来执行此操作：
```
$ sudo start smqueue
smqueue start/running, process 21101
```

### 回声短信（411）
在手机上，编号为411的短信。这是SMQueue中的一个“短代码”处理程序，它只会回显接收到的任何内容以及有关使用的网络和用户帐户的其他信息。给411的消息的主体可以是任何你想要的，尽管对每个消息或连续的数字或字母使用独特的内容可能是有用的。这可以帮助您确定发生错误时正在响应的消息。一旦你的信息组成411，点击发送。几秒钟后，应该会出现一个回复（示例如下）：
```
“1 queued, cell 0.1, IMSI214057715229963, phonenum 6055551234, at Sep 8 02:30:59, Ping pong"
```
这表示以下内容：
•有一条消息排队
•基站的负载因子为0.1
•从IMSI 214057715229963，MSISDN 6055551234接收到消息
•消息在9月8日02:30:59发送
•消息正文为“Ping pong”。 “

### 直接SMS
SMS消息也可以通过使用sendms命令直接从OpenBTS进行测试。在OpenBTS CLI中，让我们看看如何使用help命令调用它：
```
OpenBTS> help sendsms
sendsms IMSI src# message... -- send direct SMS to IMSI on this BTS, addressed
from source number src#.
```
通过指定目标IMSI，消息应该看起来源于的源号码和消息主体本身来发送消息。 用您的订户帐户替代信息编写消息并按Enter键：
```
OpenBTS> sendsms 214057715229963 8675309 direct SMS test
message submitted for delivery
```
几秒钟后，您的手机应该显示来自假想号码8675309的新的传入消息，其中包含“直接短信测试”。
以这种方式创建的SMS消息根本不会路由SMQueue; 它们通过GSM空中接口直接发送到手机，因此不能重新安排时间。 如果手机离线或无法到达，这些信息就会丢失。 这就是SMQueue需要的原因 - 尝试和重新安排在固有不可预测的无线环境中的交付。

### 双方短信
如果您配置了多个手机用于您的网络，请随时在它们之间来回发送一些消息。 验证接收消息时源号码是否正确，并将这些消息的回复路由回原始发件人。

## 测试呼叫
另一项要测试的服务是语音。与SMS一样，OpenBTS不直接处理语音，并需要额外的服务才能运行，在这种情况下，Asterisk。立即启动Asterisk：
```
$ sudo start asterisk
asterisk start/running, process 1809
```
使用您在SMS测试中使用的手机，您现在将验证语音服务的几个方面。这是通过使用rangeasterisk-configs软件包定义的一些测试扩展来完成的。分机是内部电话号码，从外面无法接通。

### 测试音调（2602）
您将使用的第一个测试扩展会播放一个不变的音调。这听起来可能不太令人兴奋，但确实证实了有关网络的许多事情：
- Asterisk正在运行并且可以访问。
- 呼叫路由按预期工作。
- 下行音频功能正常。
现在用手机拨打2602。
当你听到音调时，听音调的变化。音调的这些变化是由于丢失了下行链路语音流路径中的信息，类似于分组丢失。在现场，这是测试音调扩展的主要用途：测试下行链路质量。在生产网络中，下行链路损失3％是正常的，损失5％-7％仍然可以提供可理解的对话。

### 回声呼叫（2600）
下一个测试扩展会创建一个“回声呼叫”。基本上，Asterisk收到的所有音频将立即回送给发件人。除了确认列出的用于测试音调的项目之外，回声呼叫还会显示网络中存在的任何延迟或上行链路质量问题。
现在用手机拨打2600。
当你对着麦克风讲话时，你很快会听到自己在听筒里的声音。有点延迟是正常的，但更长的延迟会导致更像是使用对讲机的体验。人脑可以处理大约200毫秒的延迟而没有麻烦。除此之外，谈话开始瓦解，双方都停止说话，因为它变得不舒服。

### 双方通话
如果您配置了多个手机用于您的网络，请随时在它们之间拨打一些电话。接收呼叫时验证来源号码是否正确。

### 测量链路质量
此时，您应该了解OpenBTS CLI中可用的便捷工具chans命令。 该工具可用于客观量化链接质量，而不是基于用户感知。 要查看此命令有用的任何内容，必须有一个活动的呼叫。 在这个例子中，呼叫被放置到2602测试音调扩展，并且在10秒钟后执行chans命令（注意“时间”列）。 手机被移动了一米远离正在使用的无线电，另一个带有chans命令的样本被采集：
```
OpenBTS> chans
CN TN chan transaction Signal SNR FER TA TXPWR RXLEV_DL BER_DL Time IMSI type id dB pct sym dBm dBm pct
0 1 TCH/F T101 28 28.7 0.15 1.2 7 -61 0.00 0:10 2140...
OpenBTS> chans
CN TN chan transaction Signal SNR FER TA TXPWR RXLEV_DL BER_DL Time IMSI type id dB pct sym dBm dBm pct
0 1 TCH/F T101 14 31.4 0.50 1.2 19 -73 0.00 0:26 2140...
```
这里有很多领域，他们都非常有用，但现在我们来关注信噪比（SNR），TXPWR和RXLEV_DL。在两次读数中，都有一个活动频道。 SNR列表示由基站测量的上行链路的SNR：越高越好。随着手机移开，这个数字实际上有所改善！怎么可能？答案在于TXPWR专栏。该列表示手机报告的上行链路发射功率。在二读中，这个数字已经从7跳到19 dBm，这意味着手机使用更多的功率将其信号传输到基站。这可以解释为什么在基站测得的信噪比更好。取决于基站接收其上行链路信号的程度，网络独立地指示手机以不同的功率电平进行发送。这是因为所有的信号都是在基地台以大约相同的强度接收到的，这使得它更容易解调。然而，基站在下行链路上对所有的手机使用相同的发射功率。这可以在RXLEV_DL列中观察到。该列表示手机报告的下行信号电平。在二读时，随着手机远离基站移动，这个数字从-61 dBm下降到-73 dBm。它正在接收较低强度的下行链路信号，因为它现在距离较远。这些字段的完整列表可以通过运行来检索：
```
OpenBTS> help chans
```

## 配置系统（续）
在本章中，您已经熟悉了几个OpenBTS命令，其中最重要的一个可能是配置。 在继续之前，我们将提供一些关于配置的最终细节，并且会向您介绍几位亲戚。
### 配置
到目前为止，您使用config来搜索配置键并更改其值。 如果提供了完整的名称，它还可用于提供有关特定配置密钥的其他信息：
```
OpenBTS> config SIP.Proxy.SMS
SIP.Proxy.SMS 127.0.0.1:5063 (default)
- description: The hostname or IP address and port of the proxy to be used for text messaging. This is smqueue, for example.
- type: hostname or IP address and port
- default value: 127.0.0.1:5063
- visibility level: customer warn - a warning will be presented and confirmation required before changing this sensitive setting
- static: 0
- scope: value must be the same across all nodes
```

### DEVCONFIG
devconfig命令的功能与config命令相同。 但是，它允许您操作更多类型的键。 每个键都有一个可视级别，用于标识键用于用户，开发人员或工厂的人员。 使用devconfig可以访问这些更敏感的键，例如协议定时器：
```
OpenBTS> config GSM.Timer
GSM.Timer.Handover.Holdoff 10 [default]
GSM.Timer.T3109 30000 [default]
GSM.Timer.T3212 0 [default]
OpenBTS> devconfig GSM.Timer
GSM.Timer.Handover.Holdoff 10 [default]
GSM.Timer.T3103 12000 [default]
GSM.Timer.T3105 50 [default]
GSM.Timer.T3109 30000 [default]
GSM.Timer.T3113 10000 [default]
GSM.Timer.T3212 0 [default]
```

### rawconfig
rawconfig命令会比devconfig做更多的事情并删除所有输入验证。 如果你有一个你想更改为有效范围之外的实验值的键，则需要使用rawconfig。
rawconfig的另一个用途是在数据库中定义全新的自定义键/值对。 编写新功能时这很方便：
```
OpenBTS> rawconfig My.New.Setting zebra
defined new config My.New.Setting as "zebra"
```

### unconfig
一些控制可选功能的按键可以被禁用。 可以禁用的键将在其类型字段中设置“字符串（可选）”：
```
OpenBTS> config Control.LUR.FailedRegistration.Message
Control.LUR.FailedRegistration.Message Your handset is not provisioned for this network. [default]
- description: Send this text message, followed by the IMSI, to unprovisioned handsets that are denied registration.
- type: string (optional)
```

unconfig命令将尝试禁用某个密钥并在其成功时报告回来.
```
OpenBTS> unconfig Control.LUR.FailedRegistration.Message
Control.LUR.FailedRegistration.Message disabled
```

### rmconfig
要将密钥恢复为其默认值，请使用rmconfig：
```
OpenBTS> rmconfig SIP.Proxy.SMS
SIP.Proxy.SMS set back to its default value
```

该命令也可用于删除使用rawconfig定义的自定义键/值对：
```
OpenBTS> rmconfig My.New.Setting
My.New.Setting removed from the configuration table
```

## 个性化您的网络
你的网络已经启动并且正在运行，你知道如何控制它，而不是一个糟糕的开始。 然而，这是你的网络，所以它应该根据你的口味进行定制。 本节将介绍个性化一些事情的过程，以便它绝对是您的网络。
### 简称
浏览时，短名称会显示在某些手机上。 这是搜索网络时有人注意到的第一件事，所以请继续并从OpenBTS的默认设置中进行更改：
```
OpenBTS> config GSM.Identity.ShortName GroundControl
GSM.Identity.ShortName changed from "OpenBTS" to "GroundControl"
```
只有名称的字母数字字符不允许使用空格和特殊字符。

### 注册消息
如果您还记得，在第27页的“第一次连接”中，有一条“注册失败”的短信发送给手机。 实际上，对于不同的事件，有几条SMS消息。 要查看它们的列表，请使用名称中的Registration.Message搜索配置密钥：
```
OpenBTS> config Registration.Message
Control.LUR.FailedRegistration.Message Your handset is not provisioned for this network. [default]
Control.LUR.NormalRegistration.Message (disabled) [default]
Control.LUR.OpenRegistration.Message Welcome to the test network. Your IMSI is [default]
```
注册失败的消息很好，但有点无聊。 您可以使用config命令更改它：
```
OpenBTS> config Control.LUR.FailedRegistration.Message Nuh-uh-uh, you didn't say
the magic word.
Control.LUR.FailedRegistration.Message changed from "Your handset is not provisioned for this network." to "Nuh-uh-uh, you didn't say the magic word."
```

或者，要完全禁用失败的注册消息，您可以使用unconfig：
```
OpenBTS> unconfig Control.LUR.FailedRegistration.Message
Control.LUR.FailedRegistration.Message disabled
```

还有另一个消息，Control.LUR.NormalRegistration.Message，默认情况下是禁用的。 每次成功注册时，该消息都会发送到手机。 虽然在生产环境中很烦人，但它对于实验室来说可能是一个有用的工具，特别是如果他们正在操作多个基站的话。 如果注册更新或切换，该消息用作提示：
```
OpenBTS> config Control.LUR.NormalRegistration.Message Welcome to BTS 1
Control.LUR.NormalRegistration.Message changed from "" to "Welcome to BTS 1"
```
要测试它，请尝试关闭手机的电源或切换打开和关闭飞行模式。 当手机重新获得您的基站信号并注册时，您应该收到一条消息。 第6章将介绍Control.LUR.OpenRegistration.Message参数。

