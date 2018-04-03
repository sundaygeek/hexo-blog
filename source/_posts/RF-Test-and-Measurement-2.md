---
title: RF Test and Measurement-2
date: 2018-04-03 23:26:01
tags:
    - RF
    - 通信
categories:
    - 通信
    - RF
---
<continute....>

# LTE特有的测量
尽管LTE射频测量类似于其他技术有许多共同的特征，有几个测量是LTE唯一的。你能猜出这将是LTE具体测试？如果你还记得的LTE信号，我上面描述的独特性能，你会做一个很好的猜测。

**IBE（In-Band Emission 带内放射)**
 
LTE的最独特的特征之一将是，在大多数情况下，UE不使用全系统带宽，这意味着它仅使用一部分射频带宽。对于射频特性而言，RF频带的这部分使用不会引起任何严重的问题，但如果所述部分被分配给中心频率的另外一边呢？你会看到频谱如下。（我无法解释这幅画的细节。只要仔细看，并试图在这张照片看，你会知道这个意思，为什么我们需要衡量这个项目）。UE发射器设计目标是尽量减少在中心频率和其他部分（image signal）的发射。

![这里写图片描述](http://img.blog.csdn.net/20151121145609329)

**功率控制 - 绝对功率容差**
 
当UE发送第一PUSCH发送时，功率由P0-NominalPUSCH和PUSCH功率控制算法来确定。这项测试是为了检查UE是否根据P0-NominalPUSCH和PUSCH功率控制算法设置精确的初始PUSCH功率（这和我们所说的在3G网络中开环功率控制不同的，当我们说“开环功率控制”，它通常意味着初始的PRACH功率。相反，绝对功率容差约为初始PUSCH功率）。
 
![这里写图片描述](http://img.blog.csdn.net/20151121145957856)
 
**功率控制 - 相对功率容差**
 
另一个方面我想提的是关于功率的控制。总体功率控制测试方法和WCDMA ILPC（内环功率控制）测试是非常相似的。网络（SS）发送TPC命令（发送功率控制命令）的连续序列，就像Up, Up, Up, Up... Down, Down, Down, Down,... 并检查UE是否正确解码命令，并设置UE TX各UP / DOWN指令（+ 1dB，1dB）指示功率。 LTE采用了类似的方法。但是，如果你看到下面的测试，你会看到在中间有一个巨大的跳跃，这是不与WCDMA相同。这是否意味着LTE具有特殊的TPC命令状10分贝或-10 dB的跳转呢？
您在测试中看到的大的跳跃不是因为大的TPC命令的结果，而是RB的数量增加的结果。即使UE在每个子载波上发送相同的功率，如果UE正在使用更多的子载波，总功率就会变大。
 
 ![这里写图片描述](http://img.blog.csdn.net/20151121150351397)
![这里写图片描述](http://img.blog.csdn.net/20151121150412730)
 
**总功率控制容差**
 
这个测试是用来检查在无任何附加调整情况下，UL功率如何保持平稳。这个测试项是重要的，因为有在此期间，有很多种情况就像测量间隙或连接模式DRX时，UE将不能在接收任何功率控制的命令。
由于该功率稳定被应用于PUCCH和PUSCH两者，此测试是有两个子试验，一个用于PUCCH和另一个用于PUSCH，如下所示。
 
下图是PUCCH总功率控制公差。
![这里写图片描述](http://img.blog.csdn.net/20151121150921446)

下图是PUSCH总功率控制公差。
![这里写图片描述](http://img.blog.csdn.net/20151121151022594)
 
# 射频测试设备与 UE连接图
![这里写图片描述](http://img.blog.csdn.net/20151121151649559)
![这里写图片描述](http://img.blog.csdn.net/20151121151858462)
![这里写图片描述](http://img.blog.csdn.net/20151121151905415)
![这里写图片描述](http://img.blog.csdn.net/20151121151911838)
![这里写图片描述](http://img.blog.csdn.net/20151121151918384)
![这里写图片描述](http://img.blog.csdn.net/20151121151925351)
![这里写图片描述](http://img.blog.csdn.net/20151121151934946)
![这里写图片描述](http://img.blog.csdn.net/20151121151940907)
![这里写图片描述](http://img.blog.csdn.net/20151121151949589)
![这里写图片描述](http://img.blog.csdn.net/20151121151957066)
![这里写图片描述](http://img.blog.csdn.net/20151121152004345)
![这里写图片描述](http://img.blog.csdn.net/20151121152011470)
![这里写图片描述](http://img.blog.csdn.net/20151121152024320)

# 射频一致性测试
 
射频一致性测试的总体协议序列在36.508定义。但大多数的设备供应商有一个小的变化。我把下面有一个例子序列。

1) MIB
2) SIB 1, 2, 3
3) RRC : PRACH Preamble
4) RRC : RACH Response
5) RRC : RRC Connection Request
6) RRC : RRC Connection Setup
7) RRC : RRC Connection Setup Complete + NAS : Attach Request
8) RRC : DL Information Transfer + NAS : Authentication Request
9) RRC : UL Information Transfer + NAS : Authentication Response
10) RRC : DL Information Transfer + NAS : Security Mode Command
11) RRC : UL Information Transfer + NAS : Security Mode Complete
12) RRC: DLInformationTransfer + TC: ACTIVATE TEST MODE
13) RRC: ULInformationTransfer + TC: ACTIVATE TEST MODE COMPLETE
14) RRC : Security Mode Command
15) RRC : Security Mode Complete
16) RRC: UECapabilityEnquiry
17) RRC: UECapabilityInformation
15) RRC : RRC Connection Reconfiguration + NAS : Attach Accept + NAS : Activate Default EPS Bearer Context Req
16) RRC : RRC Connection Reconfiguration Complete + NAS : Attach Complete
17) RRC : RRC Connection Release
< MO or MT call > : In MT call, Paging should be sent.
18) RRC : PRACH Preamble
19) RRC : RACH Response
20) RRC : RRC Connection Request
21) RRC : RRC Connection Setup
22) RRC : RRC Connection Setup Complete
23) RRC : Security Mode Command
24) RRC : Security Mode Complete
25) RRC : RRC Connection Reconfiguration
26) RRC : RRC Connection Reconfiguration Complete
< Perform Test Case >
27) RRC : RRC Connection Release

和普通的呼叫处理协议序列不同的一个重要步骤，主要不同的部分是“TC：ACTIVE TEST MODE”。这一部分中详细地下列规格说明。
 
36.509 - 6.5 ACTIVATE TEST MODE
36.509 - 5.3.2.3 Reception of ACTIVATE TEST MODE message by UE
 
不像在UMTS测试模式，LTE射频一致性测试的测试模式不建立环回模式。 LTE中的测试模式的主要目的，在36.509中描述 - 5.3.2.3如下。
 
当UE测试模式是激活的，则UE应当接受任何请求建立数据无线承载与相关联的EPS承载上下文，既包含在相同RRC消息，并在UE的无线接入功能。
 
 
# 射频一致性测试 - TRX一致性（36.521-1）
 
似乎很多人都认为射频一致性仅用于测试手机的射频前端部分的性能/功能。这可能是事实，但这些测试的最终目的是为了确保UE在实网中的没有任何问题。所以每个测试项目都是和实网中的一个或多个步骤相关联。
因此，具有实网中UE和网络之间交互过程的大画面是非常重要的，并发现这些一致性测试项目和步骤与实网过程/步骤之间的相关性。
经常有人问我：“为什么我们需要测试这些测试项目？这些试验的目的是什么？”。如果你看到的3GPP规范（36.521），以查找对此类问题..答案被描述在“试验目的”部分，如下所示。
下面的例子是6.3 动态输出功率。

**当功率设置为最小值时，为了验证该UE是否有能力以一个低于测试设备上指定的输出功率传输。**

这是否有任何意义吗？至少对我来说，没有太多。这说明有利于测试成功或者失败的标准，但并没有给我的测试目的有任何实际意义。
 
我建议是描述整个活网络程序以高层的水平如下，并尝试各一致性测试用例来映射到您的高层描述的步骤中。
1）UE处于关闭状态 
2）UE开机 
3）<频率搜索> 
4）<小区搜索>：正常情况下，在这个过程中UE会发现多个小区 
5）<小区选择> 
6）MIB解码 
7）SIB 解码 
8）<初始RACH过程> 
9）<注册/验证/连接> 
10）<默认EPS承载设置> 
11）现在UE处于空闲模式 
12）<（如果当前的电池变弱或UE移动到另一个小区注册）小区重选> 
13）<（当寻呼消息到来或用户拨打电话）RACH过程> 
14）<设置专用EPS承载> 
15）接收数据 
16）发送数据 
17）（如果网络接收到UE功率太弱）网络发送TPC命令来增加UE Tx功率 
18）（如果网络接收到UE功率太强）网络发送TPC命令减少UE Tx功率 
19）<（如果UE移动到另一小区区域）的网络和UE执行切换过程> 
20）用户停止呼叫，UE进入空闲模式


如果你只是一味地寻找到一致性测试规范，你可能会对大量的测试用例的数量不知所措。有没有可能让我理解所有这些？我必须放弃挖掘细节，只按测试系统上的按钮，并采取盲目测试而不管机器吐出来什么东西？
但是，一旦你得到了这些测试用例的大图，你会发现，所有这些测试用例可以分组称为这个框架的一部分。
 
首先我对你的建议是去理解每章的概念（大图），而不是直接跳跃到每个测试用例。
 
让我们来看看TRX一致性36.521-1的每个章节。这四个章节都与上述的3）5）19）相关联的。
第6章，第7章是直截了当，他们是测试最广泛的，从开发阶段到生产线的测试。看到第8章的标题，你可能会得到一点点的混淆，当我们说“性能”，很多人会认为“IP吞吐量”。但在这种情况下，它是不正确的。在这种情况下，“性能”是指“UE如何在各种嘈杂的信道条件成功解码下行链路信号”。第9章是衡量“UE如何精确地测量各种信道条件，并报告给网络？”。总之，它是测量CQI（信道质量指示符），RI（秩指数），PMI（预编码矩阵索引）。
![这里写图片描述](http://img.blog.csdn.net/20151121154136289)

现在，让我们看看在每章每一个测试用例。
第6章（发射机特性）有以下测试用例。正如你看到的，它只是衡量UE发射功率的不同情况和数据调制质量。大部分的测试项目，是LTE独有的。

第7章几乎是和WCDMA相同的接收机的测量。基本上这些测试用例都是测量多少数据被正确解码，在各种UE的接收机功率（网络传输功率）多少数据丢失。

第8章主要是测试关于各种嘈杂/衰落信道条件下，UE如何解调重要的下行信号（例如，PDSCH，PCFICH/ PDCCH，PHICH）。正如你可能已经猜到，这些测试用例是用像A9，A10，A11的测试系统配置（如前面的章节（TS36.508附录A））。

第9章是用于测量UE关于无线信道状态各种反馈（CQI，RI，PMI）。对于这个测量，我们要模仿各种信道条件，所以我们需要使用像A9，A10等测试配置如前面的章节（TS36.508附录A）

# 射频一致性测试 - RRM一致性（36.521-3）
![这里写图片描述](http://img.blog.csdn.net/20151121164425240)

# 射频/物理层性能测试概述
 
性能测试RF/ PHY的基本理念是非常简单的。正如在下面的图中所见，一个DUT（UE）连接至SS（信令模拟器），单个小区和衰减器和AWGN，被连接到下行链路路径。
目的是测试UE如何能够在恶劣的信道条件解码的各种下行链路物理信道（例如，在AWGN和/或衰落的存在）。

![这里写图片描述](http://img.blog.csdn.net/20151121164702118)

你可以找到来自36.521-18性能要求的性能测试的细节。有此章中相当多的测试用例如前一节的表格，但我们可以简化整套测试在一个简单的表如下。
 
**测试信道（UE必须解码的信道）**
PDSCH，PCFICH/ PDCCH，PBCH，PHICH
**测试参数（测试条件）**
Rho A，Rho B，NOC，调制方式
**衰落条件**
EVA5，ETU70，ETU300，HST
**SNR**
-1.0〜19.1
 
注意：如果你有兴趣更详尽的测试，是指物理层吞吐量/性能测试（我个人的测试）

![这里写图片描述](http://img.blog.csdn.net/20151121164927871)

![这里写图片描述](http://img.blog.csdn.net/20151121165031959)

《end》