---
title: GSM帧结构
date: 2018-04-03 23:23:30
tags:
    - GSM
    - 通信
categories:
    - 通信
    - GSM
---
原文链接：
http://www.sharetechnote.com/html/FrameStructure_GSM.html

# 帧结构概述

当我第一次学习GSM的时候，我对帧结构感到很迷惑。主要的一个原因是整个结构的每一个周期都有需要实习和帧，很难在一张纸上形象化地画出整个帧结构。
我没有找到任何形象化描述整个超帧中每一个时隙的方法，但是GSM帧层次图如下。
你必须详细的理解时隙，帧，多帧，超帧。

![这里写图片描述](http://img.blog.csdn.net/20151114132200288)

**有用的视频连接**
视频连接是YouTube上的，国内的小伙伴还是翻墙看吧。
Teleysia - GSM TDMA Frame 1 
http://www.youtube.com/watch?v=HZtBIcUTop8&feature=related

Teleysia - GSM Logical Channels 1 
http://www.youtube.com/watch?v=wwEO_muXD2M

# 每一种Burst的结构
每一种无线通信中，我们都会使用不同种类的信道类型。这些不同信道具有他们自己的物理（或者传输）结构。在GSM中，也有不同种类的物理和逻辑信道，这些信道在时隙层次上都是用他们自己的物理结构。下面是一些GSM/GPRA中使用的不同burst类型。

![这里写图片描述](http://img.blog.csdn.net/20151114132803243)

每一种GSM/GPRS物理信道都映射成这些burst类型。每一种物理信道也都映射为高层逻辑信道。
这些信道的映射见其他文章描述。

##  < Frequency Correction Burst >频率校正突发脉冲序列

 - 这个burst仅仅用于FCCH信道
 - 整个数据区域有142bit，用于承载非调制的再摸（纯正弦），或者承载全零的调制载波。
 - 频率为1625/24kHz（或者接近67kHz）
 - 这个纯载波是beacon频率（定标频点，或者叫BCCH频率，基准频率）和FCCH时隙的标识。

##  < Synchronization Burst >同步脉冲序列

 - 这个burst仅仅用于SCH信道
 - 这个信道使手机和基站时钟保持时间同步。这也是相对于其他burst来说，这个burst的训练序列这个长的原因。
 - 在这个burst中仅仅定义一个训练序列

##  < Access Burst >接入脉冲序列

 - 这个burst用于RACH和AGCH信道

当手机发送RACH消息，并接收到AGCH回应，MS和BTS都没有时间提前信息。但是实际消息是相对较短，并且有较长的保护间隔（GP guard band），这是为了确保和下一个burst之间不会有重叠。在接入脉冲序列中保护间隔的长度（68.25bit x 3.69 = 251.16ms）是相当于37.5km传输时延。GSM允许小区半径是35km。也就说从最大距离基站35km的MS发送的RACH消息到达基站天线时是和下一个burst没有重叠的。FACCH信道是在切换操作的时候使用这个burst（当新小区的时间提前信息不知道的时候）。在这个burst中仅仅定义了一个训练序列。


##  < Normal Burst >正常脉冲序列

 - 这个burst用于其他除FCCH/SCH/RACH/AGCH之外的所有的信道
 - 正常burst用于TCH/SDCCH/SACCH/FACCH/BCCH/PCH

这个burst有一些重要的特征：

 - 一个burst中最大传输57*2=114bit的语音或者数据
 - Flag bit是用来只是这个信道是携带的用户负载（flag=0）,或者是控制消息bit（flag=1）,也就是说，在TCH中，flag=0。在其他信道中，Flag=1。


##  < Dummy Burst >哑脉冲序列
这个burst像正常脉冲学列，但是没有任何有意义的bit传输。


# 多帧的帧结构

GSM中有两种类型的多帧。一种叫控制多帧，另外一种叫负载多帧。
（slot--frame--multiframe(control or traffic)--super frame--hyperfame）

下面一个示例描述**控制多帧**。每一个multi-frame有51x8个时隙，值得注意的是在每一帧的第一个时隙都用于控制信道。在这个图中，每一帧的第二个时隙用于控制信道，但是不总是这样。

![这里写图片描述](http://img.blog.csdn.net/20151114135727168)

# 通信中时隙分配

这个实例中的部分参考下面的文档。
http://www6.conestogac.on.ca/~mkabir/TCOM1010_ConEd_Cellular/GSM%20Phy%20Part-1.pdf
http://www.teletopix.org/gsm/how-26-and-51-multiframes-in-gsm/

下面的图展示了时隙层面上的结构。可以看到，最小的单位是时隙，8个时隙组成一帧。
![这里写图片描述](http://img.blog.csdn.net/20151114140217070)

上面的图示很接近于实际物理信号流。但是很难描述出不同的调度信息，因为基本的调度室基于帧为单元的。

下面是一个**traffic多帧**的实例。**有26个多帧用于TCH/FR和TCH/ER。**

这个实例描述了两个用于在使用全速率语音信道FR。（一个用于使用每一帧的时隙2，另一个用户使用每一帧的时隙4）。在26帧traffic多帧的中间，也就是Frame-12，是SACCH，携带了ME和BTS之间的链路控制信息。最后的帧是一个空帧。所有剩余的帧都分配了负载。没有专用的FACCH帧或者时隙。FACCH会在需要的时候偷取TCH帧。
![这里写图片描述](http://img.blog.csdn.net/20151114140950128)

下面的这个是用于半速率语音信道HR的26多帧结构，两个用户使用HR语音。在这个实例中，两个用户交替共享每一帧中的时隙2。你可以注意到有两个SACCH。帧12用于用户1的SACCH，帧25用于用户2的SACCH。
![这里写图片描述](http://img.blog.csdn.net/20151114141701444)


----------
控制信道的多帧结构更加复杂，结构会根据控制信道类型而变化。但是一个公共的地方是所有的控制信道帧结构都是基于51多帧的。

下面是Beacon信道（base信道）的控制系电脑多帧结构，它是由FCCH，SCH，BCCH，CCCH组成。图中描述的是下行多帧结构。对于上行base信道中，每一帧都是用于单一信道--RACH。下行base控制信道的主要特点有：

 - 在51多帧结构中有5个等空间的FCCH
 - 每一个FCCH后面跟随者SCH，意味着同样有5个SCH
 - 四个帧（帧2-5）都用于BCCH
 - 最后一个帧（帧50）是空的
 - 所有剩余的帧都分配给CCCH（eg，PCH or AGCH）

![这里写图片描述](http://img.blog.csdn.net/20151114142326798)

上面的实例真是的独立信道。（例如，传输信道和控制信道相互独立的情况）。实际上，这些调度是融合在一起的，形成一个复杂的表格。

![这里写图片描述](http://img.blog.csdn.net/20151114142748387)

下面引用“GSM Phy part-1”中的描述。
GSM的BTS有一个或者多个GSM频率信道（ARFCN）。这些频率信道使用base-frequency（beason频率或者BCCH频率）定义的。base-frequency的第一个时隙用于base-control信道，这个频率信道剩余的部分（slot1-7）用于传输和控制信道的混合。所有的其他频率都是用于traffic，但是也可以用于控制信道。traffic和控制信道的混合取决于每一BTS的频率信道数和traffic模式。
例如：

 - 很少电话需要很少的RACH信道
 - 短电话需要很少的TCH
 - 高traffic小区有大量的频率信道，可能base-frequency信道没有traffic信道。



