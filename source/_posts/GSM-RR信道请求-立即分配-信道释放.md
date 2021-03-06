---
title: GSM RR信道请求/立即分配/信道释放
date: 2018-04-03 23:24:44
tags:
    - GSM
    - 通信
categories:
    - 通信
    - GSM
---
# RR  : Channel Request

信道请求具有各种功能，在上电之后的信道请求相当于UMTS和LTE的RACH过程。简单地说，这个消息是告诉网络“我想驻留上你，我开始连接过程，请分配无线资源”。
一个用于初始注册如下。 （在良好的信道状态，以及在开放的空间中，第一个信道请求将到达网络并获得立即分配，只有一个信道请求。以下为一种最坏的情况下的，其中初始信道请求不能由NW解码或UE对初始直接分配解码失败）

![这里写图片描述](http://img.blog.csdn.net/20151121195956641)

注：在“信道请求”的最后一个重传时启动T3126，并在UE得到“立即支配”时停止。如果UE没有得到“立即支配”，直到T3126到期时，UE开始小区重选。
 
一个信道请求的确切功能由消息的第一个8位和第一字节的描述中，由下表中44.018中指定决定。
![这里写图片描述](http://img.blog.csdn.net/20151121200250597)


# RR  : Immediate Assignment

立即分配是对最初的“信道请求”的应答消息。这就是说，以MS“好吧，我会接受你的‘信道请求’，你可以使用以下步骤获得无线资源”。
一个信道请求的例子将如下所示。你必须要注意的最重要的事情是，立即指派应该重放的整个信道请求消息。所有的MS（UE）的尝试连接的小区将接收相同的“立即指派”，但是，如果包含在“立即指派”的'信道请求”部分与它送出信道请求字节不匹配，UE应当丢弃“立即指派”消息，因为它不是对本身。
![这里写图片描述](http://img.blog.csdn.net/20151121200608855)

# RR：Channel Release
GSM信道释放是基于44.018 3.4.13.1.1，信道释放过程是在专用模式和组传输模式下发起的。
 
**什么时候使用？**
 

 - 在呼叫释放后释放TCH
 - 当分配给信令的专用信道被释放时用以释放DCCH
 - 去激活SACCH

**UE在收到信道释放之后应该怎么做？**
 
UE启动定时器T3110并断开主信令链路。当T3110超时，或当确认被断开时，移动台停用所有信道，认为RR连接已经释放，并返回到CCCH空闲模式，返回到PCCCH或CCCH分组空闲模式或进入数据包传送模式。
 
 
**信道释放原因**
 
有许多释放原因如下所示，但只有其中的一些在44.018 3.4.13.1.1中描述。我会继续增加更多的说明。
![这里写图片描述](http://img.blog.csdn.net/20151121202600028)

**在信道释放消息中重要的IE**
 
以下为信道释放进行整体结构和IE（信息元素）。
![这里写图片描述](http://img.blog.csdn.net/20151121221312886)
BA范围：
一个UE在其小区选择算法可以使用这个字段（参见3GPP TS45.008和3GPP TS23.022）。
所有TCH的释放和SDCCH后的小区选择指示：
移动台的所有TCH和SDCCH释放后在小区选择算法可以使用该指示（参见3GPP TS45.008）
Individual priorities：移动台可以用它来传达单独优先级信息到MS（见第3.2.3.3）。当MS接收到一个单独优先级IE应启动定时器T3230的一个实例，在个人的优先级IE提供的值。
