---
title: LTE 参数CQI RI PMI
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---
原文链接：
http://lteuniversity.com/get_trained/expert_opinion1/b/hongyanlei/archive/2009/08/06/cqi-reporting-in-lte.aspx


CQI = Channel Quality Indicator;信道质量指示; 
RI = rank indication;秩指示;
PMI = Precoding Matrix Indicator;预编码矩阵指示;

CQI用来反映下行PDSCH的信道质量。用0~15来表示PDSCH的信道质量。0表示信道质量最差，15表示信道质量最好。
-->UE在PUCCH/PUSCH上发送CQI给eNB。eNB得到了这个CQI值，就质量当前PDSCH无线信道条件好不好。 这样就可以有根据的来调度PDSCH。
-->换句话说，LTE中下行的自适应编码调制（AMC）的依据是什么？其中一个依据就是CQI。
-->再通俗一点的说法：信道质量好，那eNB就多发送点数据；信道质量不好，那就保险点，少发送点数据。

RI用来指示PDSCH的有效的数据层数。用来告诉eNB，UE现在可以支持的CW数。也就是说RI=1，1CW，RI>1，2 CW. CW-Code Word码字

PMI用来指示码本集合的index。由于LTE应用了多天线的MIMO技术。在PDSCH物理层的基带处理中，有一个预编码技术。
-->这里的预编码简单的说，就是乘以各种不同的precoding矩阵。而这个矩阵，可以采用TM3这样没有反馈的方式。
-->也可以采用TM4这样通过UE上报PMI来决定这个预编码矩阵。从原理上说，这样使得PDSCH信号是最优的。

下行的传输模式(TM)很多，在R9版本下行定义了TM1~TM8；其中TM4,6,8的情况下，才需要有PMI的反馈。

更多详细的内容参见36.211、213。

CQI（信道质量指示）报告是LTE的一个重要因素，并具有对系统性能显著影响。有两种类型的CQI报告在LTE中：周期性和非周期性。周期性CQI报告是由PUCCH携带。但是，如果UE需要在同一子帧发送的UL数据，周期性CQI报告将使用的PUSCH，连同UL数据传输。这是因为UE不能PUCCH和PUSCH的同时发送。在此情况下，周期性PUCCH资源将是空闲的。由于定期CQI报告带来了“永远在线”的信令开销，报告粒度相对粗糙。为了获得更详细的CQI报告，则eNB可以在需要时触发不定期CQI报告。非周期性CQI报告被发送PUSCH上，连同UL数据或独自发送。

CQI报告的粒度可分为三个层次：宽带，UE选择的子带，和更高层配置的子带。宽带报告为整个下行链路系统带宽一个CQI值。该UE选择子带CQI报告将系统带宽划分为多个子带，选择一组优选子带（最好的M个子带）中，然后报告了宽带和一个差分CQI值设定一个CQI值（假设传输只能通过选择M个子带）。高层配置的子带报告提供了最高的粒度。它将整个系统带宽划分为多个子带，然后报告一个宽带CQI值和多个差分CQI值，每个子带一个。

在闭环MIMO的情况下，PMI（预编码矩阵指示符）和RI（秩指示）也报告。基于对接收的参考信号的评价，PMI指示在eNB应该使用多个天线进行数据传输中的码本（预先约定的参数）。 RI指示传输层，该UE能够区分的数目。只有当RI> 1，空间复用可被支持。对于空间复用，CQI是基于每码字的报告。码字的LTE中的最大数目是2。

在LTE中有七种传输模式，每一个对应于某多天线技术。对于每个传输模式下，CQI报告的某些组合在规范中定义的基础上，定期/不定期，宽带/ UE选择的子带/更高层配置的子带，没有PMI /单PMI /多PMI。因为RI变化比CQI / PMI慢，所以用一个较长的时间间隔报告。它与CQI / PMI报告一起在非周期性的报告，因为PUSCH资源是有限的。
