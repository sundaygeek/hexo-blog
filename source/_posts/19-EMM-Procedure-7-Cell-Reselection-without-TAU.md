---
title: 19-EMM Procedure 7. Cell Reselection without TAU
date: 2018-04-03 22:35:25
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6322


# I. Introduction

通过前面三篇文档，我们学习了EMM case-6 handover without TAU。这篇文档我们描述在idle模式下的UE是怎么重选小区的。
切换控制的是连接态的UE的移动性，小区重选是控制idle状态下UE的移动性。在切换时，是网络决定要切换到哪个小区，在小区重选中，是UE决定驻留在哪个小区。

小区重选过程可能是图1中的两种类型之一。在图1中UE驻留在cell-5，属于MME分配的{TA1，TA2}这个TA列表里，并处于idle状态。

 - **cell reselection without TAU**（7）：UE移动到TA列表中的一个TA时，例如图1的TA2，执行小区重选但是不执行TAU。
 - **cell reselection with TAU**（9）：UE移动到不属于TA列表中的一个TA时，例如图1的TA3，在小区重选后，需要执行TAU。

![这里写图片描述](http://img.blog.csdn.net/20151010115241011)

UE可能重选到一个小区i）和当前驻留的服务小区使用相同的LTE频率，ii）和当前小区不使用相同的频率，iii）使用另外的RAI接入（inter-RAT）。在图1中小区重选的实例中，我们只讨论intra-frequency小区重选，即使用同单一LTE频率。
这篇文档只描述case-7的情况。case-9的情况在下面的文档中叙述。在第二章，我们将描述小区重选过程需要的准备信息。第三章描述不带TA过程的小区重选的详细过程。

根据之前的LTE文档，当UE在attach完成后处于idle模式下（EMM-Registered, ECM-Idle, RRCIdle）:

 - UE IP地址保持有效，在EPC中EPC承载保持有效。
 - EPC（MME）知道UE的存在，但是E-UTRAN不知道。
 - UE是在TA级别被MME知道的
 - 当UE移动到不在TA列表的TA时执行TAU过程，并给MME通知自己的TA位置。
 - UE cannot send it generates？？？？？
 - 如果有发往UE的数据，MME在UE所在的整个TA区域内寻呼。
 - UE周期性的唤醒并检查是否有寻呼消息。

当驻留在一个小区上，UE在idle状态：

 - 接收eNB广播的系统消息
 - 接收寻呼信号，并对paging消息响应
 - 周期性的测量服务小区的信号强度
 - 当小区重选触发时测量邻区的信号强度并重选到一个小区
 - 如果重选到一个不在TA列表中的TA时发起TAU过程。

# II. Cell Reselection: Required System Information and Criteria

在我们详细学习第三章的小区重选过程之前，我们先简要介绍一下小区重选。接着我们学习这个过程需要的系统信息和小区重选的准则。

##  2.1 小区重选过程概述
UE控制小区重选过程。UE从eNB广播的系统消息中获得小区重选相关的信息（决定是否测量邻区信号强度的门限值，计算服务小区和邻区的排行的参数）。在intra-frequency小区重选中，需要的信息包含在SIB3和SIB4中。

**小区重选触发**
**服务小区测量**：idle状态的UE在每一个DRX周期的结尾处醒来测量服务小区的信号（Qrxlevmeas）并计算服务小区的接收信号水平（Srxlev）来决定是否待在这个当前小区还是移动到另外的小区。这里，UE的发射和接收条件都反映在计算中，例如通过应用最小接收信号水平（Qrxlevmin）允许最大发射功率水平Pemax等等。
**小区重选触发**：如果服务小区的接收信号水平（Srxlev）比指定的门限值（s-IntraSearch）搞，UE待在当前的服务小区。如果不是，UE触发小区切换过程。这个门限值通过SIB3传输作为触发判决条，在rel-8中定义为s-IntraSearch，在rel-9中定义为s-IntraSearchP和s-IntraSearchQ。

当UE处于相同的位置，在idle状态的UE在每一个DRX周期结尾醒来去测量服务小区的接收信号水平，我们假设UE驻留在相同的服务小区，因为这个小区的接收信号水平比设定的门限s-IntraSearch持续的低。如果UE离开这个服务小区，这个小区的接收信号水平就会很快的降低，最终，当接收信号水平低于门限s-IntraSearch时，触发小区重选过程。接着UE开始测量邻区的信号强度。

**小区重选准则**
**小区排序准则**：UE基于服务小区(Qmeas,s)和邻区(Qmeas,n)测量信号强度来对每一个小区排序(Rs, Rn)。小区排序需要的参数从SIB3和SIB4接收。服务小区使用SIB3中的q-Hyst这个迟滞值来排序，而邻区使用SIB4中的q-OffsetCell这个偏移值进行排序。
**小区重选**：一旦服务小区和邻区排序，UE检查小区重选准则是否满足(Rn > Rs)。如果邻区满足这个准则，UE选择最好的满意小区，接着驻留在那里。小区重选仅仅在(t-ReselectionEUTRA)时间内持续满足才会执行。

移动运营商会阻止太频繁的小区重选，通过这个迟滞值和小区特定偏移值可以通知UE在服务小区的驻留时间，根据这个小区状态来执行重选。另外，他们也会控制 q-Hyst and t-ReselectionEUTRA通过根据UE的移动速度来使用合适的(q-hystSF, t-ReselectionEUTRA-SF)。

##  2.2 系统信息
系统信息是由eNB广播的信息，包括MIB和SIB。MIB，SIB1和SIB2是必须的，其他的是可选的。所有的系统信息像MIB SIB1或系统信息都是通过RRC消息传送给UE的。一个系统信息包括一组SIB（SIB2-16），除了MIB和SIB1。

UE基于eNB广播的系统消息来执行小区重选的。MIB SIB1 SIB2是适用于所有的UE，不论是connected模式还是idle模式下。相反的，SIB3-8仅仅只用于idle模式下的小区重选。表1描述了不同的系统信息和他们的参数。

![这里写图片描述](http://img.blog.csdn.net/20151010115401979)

我们假设两个移动运营商A和B，他们的网络工作如下：
A：全国的LTE-only网络
      LTE 频率：在1.8GHz仅仅有10MHz的一个信道(lteFA1)
B：有全国的3G和LTE网络
      3G频率：2.1GHz上的5MHz的六个信道
      LTE频率：1.8GHz上的10MHz的一个信道，850MHz上的10MHz的另一个信道(lteFA1, lteFA2)

对于运营商A，它的eNB需要小区重选的intra-frequency相关的信息（eNB广播SIB3，SIB4，但是不广播SIB5/6/7/8）另外，对于运营商B，它需要所有的三种类型的重选信息，intra-frequency，inter-frequency，inter-RAT相关的信息（eNB广播SIB3/4/5/6）

由eNB广播的系统是通常应用于所有的UE，但是每一个根据自己的能力（release9 UE还是release11UE）来接收不同类型的系统信息。

图1描述了UE驻留在eNB2下的cell-5和它的邻区，图2只显示了eNB。

在图1中的网络时LTE-only网络，使用但是home PLMN的单一频率(lteFA1)。这篇文档只关心intra-frequency的小区重选过程。和intra-frequency重选相关的系统信息是通过SIB3和SIB4广播的。表2描述了这两个SIB的参数，但是他们和其他频率，RAT，VPLMN无关。

![这里写图片描述](http://img.blog.csdn.net/20151016084908158)

##  2.3 小区重选准则
尽管不在本篇文档范围内，下面我们将简要看下在UE开机后小区重选使用的准则。下面只适用一个准则。表3提供了这个准则中参数的详细描述。

![这里写图片描述](http://img.blog.csdn.net/20151010115602870)
在初始小区选择是，小区接收限号水平(Srxlev)必须比Qrxlevmin和P
Compensation的和要大。Srxlev要比Qrxlevmin要大，因为为了使UE正确的接收服务小区的信息，测量的信号强度必须至少大于Qrxlevmin。UE的发射功率低于UE在小区中允许的最大值Pmax将导致P-Compensation变大，使得更难选择这个小区。当选择一个小区时，UE的发射和接收条件是在考虑范围的。

在release9的情况下，增加量Squal作为小区选择准则。Qrxlevmeas是小区参考信号接收功率RSRP，而Qqualmeas是小区的参数信号接收质量RSRQ。RSRP只是接收信号的强度，而RSRQ提供关于无线链路质量更准确的信息，因为它指示信号和干扰噪声比，SINR。

**小区重选触发**
UE一旦驻留在服务小区将一直驻留在那儿，如果服务小区的接收水平满足2-1或者2-2，UE将不需要进行测量。

![这里写图片描述](http://img.blog.csdn.net/20151010115635147)
在上述的不等式中，SintraSearch, SintraSearchP and SintraSearchQ是通过SIB3给出的，如果服务小区不满足前面的准则，即是它满足3-1或者3-2准则，UE开始为了小区重选进行小区测量。
![这里写图片描述](http://img.blog.csdn.net/20151010115711253)
**小区重选准则**
**小区排序准则**
如果服务小区的测量Srxlev满足3-1或者3-2，UE开始测量邻区的RSRP，基于测量的结果，UE会应用4-1或者4-2进行排序。

![这里写图片描述](http://img.blog.csdn.net/20151010115743324)
**小区重选**
如果有多个小区满足下面的准则，UE会选择最好的小区，并驻留在上面。

![这里写图片描述](http://img.blog.csdn.net/20151010115814083)
像在之前4=1或者4-2看到的，Rs和Rn是使用不同的准则进行计算的。Qhyst and Qoffset这两个值越大，UE停留在服务小区的时间越长。

# III. procedure for cell reselection without TAU
图3描述了EMM case-7，在图1中，UE选择cell-5满足1-1或者1-2，在通过cell-5初始附着到网络后由MME分配了一个TA列表{TA1, TA2}。接着在cell-5被服务，转移到idle状态，并一直驻留在cell-5。
图3描述了驻留在cell-5上的UE怎么执行intra-frequency小区重选转移到eNB4下的cell-10的。这里UE的移动性状态为normal，所以不考虑比例因子。需要测量邻区4/6/10/13，但是图3为了描述的方便仅仅显示了cell-10和cell-13。第三章我们将详细秒后苏intra-frequency小区重选过程。必须满足一下条件。

 - 驻留在服务小区：UE驻留在cell-5
 - 小区重选触发：因为UE移出服务小区，小区重选触发。1、服务小区测量：服务小区的测量决定是否测量邻区，2、邻区测量：为了小区重选要测量邻区。
 - 小区重选准则：1，小区排序：小区基于服务小区和邻区的测量结果来进行排序，2，小区重选：识别出满足准则的小区，并选择最好的。
 - 驻留在新的小区，UE驻留在cell-10.

![这里写图片描述](http://img.blog.csdn.net/20151010115915205)

**1）【UE】UE驻留在服务小区**
UE驻留在哎服务小区并保持idle状态

**2）【UE】从服务小区获得系统信息**
UE从服务小区获得关于小区重选的系统信息。如果每一个邻区和服务小区由不同的偏移值，服务小区通过SIB4提供给UE邻区列表。接着UE通过SIB3和SIB4获得下面的信息：

 - 决定小区重选触发需要的参数：q-RxLevMin, p-Max, sIntraSearchP, s-IntraSearchQ, t-ReselectionEUTRA, q-QualMin (SIB 3)
 - 服务小区排序需要的参数：q-Hyst (SIB 3)
 - 邻区排序需要的参数：q-OffsetCell (SIB 4)

**3）【UE】测量服务小区的信号强度**
在每一个DRX周期结尾处，UE醒来并测量服务小区的信号(RSRP and RSRQ)获得Qrxlevmeas and Qqualmeas。接着，基于他们，计算出小区重选接收水平Srxlev和小区重选质量水平Squal。UE通过应用3-1或者3-2，检查UE应该重选到那个小区或者继续驻留在当前小区。例如，如果Srxlev和Squal不满足准则，UE继续驻留在当前小区，否则，执行下一步。

**4）【UE】测量邻区**
UE测量具有和当前服务小区相同频率的邻区的RSRP。

**5）【UE】小区排序准则**
一旦测量完RSRP，UE对服务小区和邻区进行排序，服务小区的排序R5，邻区的排序R4/6/10/12分别基于4-1和4-2。

**6）【UE】小区排序比较**
现在UE比较R5和Rn，检测准则5是否满足。如果每一个小区满足5，UE继续驻留在cell5，在途中，cell10和cell13满足准则。

**7）【UE】选择一个新小区**
UE比较两个满足准则的小区，R10和R13，并选择最好的一个R10作为新的服务小区。

**8）【UE】驻留在新的小区上**
UE驻留在cell10上，在接收cell10广播的SIB1后，UE知道cell10所在的TA在TAI列表中。因为这个新的服务小区主语UE注册过的TA列表，不需要执行TAU过程。接着，UE在每一个DRX周期结尾处醒来监控cell10的系统信息和寻呼信息，并测量cell10的信号。

# IV. EPS Entity Information: Before/After Cell Reselection without TAU

这章描述在小区重选过程前后EPS实体信息的变化情况。在重选过程前后UE在idle状态下，MME也保持在idle状态下。UE从cell5移动到cell10，没有执行TAU过程。在小区重选后，EPS实体中的信息保持不变，和在S1释放后保持的信息相同。如图4所示。


![这里写图片描述](http://img.blog.csdn.net/20151010120002699)

# V. Closing

我们学习了UE在idle状态下移动到UE注册过的TA中，并在不执行TAU的情况下进行小区重选过程。这个文旦仅仅描述了intra-frequency过程。多数的移动运营商有多个LTE频点。他们也可以和2/3G进行互操作，所以不仅仅有intra-frequency小区重选，而且还有inter-frenqucy和inter-RAT小区重选。在接下来的文档中，我们将讨论执行TAU的小区重选过程。

# References
[1] Netmanias Technical Document, “LTE EMM Procedure 6. Handover without TAU – Part 1. Overview of Handover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6224
[2] Netmanias Technical Document, “LTE EMM Procedure 6. Handover without TAU – Part 2. X2 Handover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6257
[3] Netmanias Technical Document, “LTE EMM Procedure 6. Handover without TAU – Part 3. S1 Handover”, March 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6286
[4] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[5] 3GPP TS 36.304, “Evolved Universal Terrestrial Radio Access (E-UTRA); User Equipment (UE) Procedures in Idle Mode”
[6] Netmanias Technical Document, “LTE EMM Procedure 8 & 9. Handover and Cell Reselection with TAU”, 2014, TBD
[7] Netmanias Technical Document, “LTE EMM and ECM States”, September 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5909
[8] 3GPP TS 36.331, “Evolved Universal Terrestrial Radio Access (E-UTRA); Radio Resource Control (RRC); Protocol Specification”
[9] 3GPP TS 36.101, “Evolved Universal Terrestrial Radio Access (E-UTRA); User Equipment (UE) Radio Transmission and Reception”
[10] Netmanias Technical Document, “LTE EMM Procedure 3. S1 Release”, January 2014, http://www.netmanias.com/en/?m=view&id=techdocs&no=6110
[11] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010