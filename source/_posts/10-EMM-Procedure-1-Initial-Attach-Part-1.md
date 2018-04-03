---
title: 10-EMM Procedure 1. Initial Attach Part-1
date: 2018-04-03 22:19:07
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---

EMM Procedure 1. Initial Attach
Part 1. Cases of Initial Attach

原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=6098

----------


# I. Introduction

这个文档讨论初始附着，也就是第九篇文档所讨论的case1。在这个阶段，用户在签约到一个LTE网络服务之后，用户开机，试图附着到网络上，发送IMSI给网络。
初始附着过程随着环境发生变化。可以是下面将要讨论的case1也可以是EMM case11（在另外一个城市初始附着，将在后面的文档中讨论）。或者可以使其他类型，取决于用户是否保存着上次附着到网络时使用的信息，（这里是指最后附着信息，或者MME是否有用户的信息，包括UE ID，接下来都是指最后一次UE上下文）。所以这个文档描述了不同初始附着类型，发现他们的特征和不同。接下来的文档part2将描述EMM case1，详细介绍相关的过程。

这个文档组织如下：第二章描述不同的初始附着类型，第三章通过列出MME执行的功能来简要讨论每一种情况的不同初始附着过程。

# II. Cases of Initial Attach

当UE初始附着到网络，MME根据附着的类型来发起不同的过程。这个过程以attach request消息开始，以attach accept消息结束。当UE发送给MME attach request消息，在这个消息包含UE ID（IMSI or old GUTI）来识别UE。当MME发送attach accept（GUTI和TAI列表）消息，在消息中包含用来代替IMSI的ID --GUTI，和包含TAI列表（在这个区域，UE不需要TAU过程可以进入的区域）。

在接收到attach request消息之后，发送attach accept消息之前，MME可能执行以下过程的全部或部分：

 - UE ID 获取
 - 鉴权
 - NAS安全建立
 - 位置更新
 - EPS会话建立

基于UE发起的初始附着的类型，决定要执行哪些过程。但是，UE ID过去和EPS会话建立过程是所有初始附着过程都必须的。其他过程，像鉴权，NAS安全建立和位置更新更具初始附着的类型选择的执行。这些过程的选择受到下面因素的影响：i)UE有的UE ID是什么（IMSI or old GUTI），ii）上次附着信息是否依旧保存在MME中有效等等。在这个文档中，我们将使用下面的判决来分辨初始附着的不同类型，如图1所示。

 - UE使用什么UE ID累发起初始附着请求（IMSI or old GUTI）？
 - UE尝试附着的MME是哪个（是上次附着的那个，还是从来没有附着过的一个新的）？
 - 在网络中是否有有效的UE上下文存在？

![这里写图片描述](http://img.blog.csdn.net/20150915135257809)

在这个文档中，如果上次UE上下文（包含UE ID）在网络中不存在，UE被定义为未知UE，其他的称为已知UE。2.1节描述未知UE的初始附着，2.2节描述已知UE的初始附着。下面，如果UE有上次附着信息，我们假设attach request消息已经完整性保护的。

## 2.1 未知UE

图2描述了当UE给网络发送了attach request消息初始附着的实例，MME没有关于用户的有效的UE上下文。我们将分辨初始附着的类型，并解释每一种类型的特征比较。现在UE想附着的MME称为新MME，上次UE已经附着过的MME称为旧MME。

![这里写图片描述](http://img.blog.csdn.net/20150915135947039)

**attach case-1：当UE使用IMSI来附着**

这个是在UE和MME中都没有上次UE上下文，这种情况下需要的场景如下：

1）UE使用IMSI作为UE ID发送给MME attach request消息。MME从这个消息中获得IMSI。
2）假设MME不知道UE（以为发送的而是IMSI），MME发起鉴权和NAS安全建立过程。
3）MME向HSS发送位置更新消息通知HSS UE已经注册上了，并从HSS中下载这个用户的签约信息。

**attach case-2：当UE附着的MME是上次已经附着的MME（new MME=old MME），但是MME没有包含有效的上次UE上下文**

这个是当UE在上次附着后仍保留着上次附着信息（old GUTI和NAS安全上下文）时UE尝试附着到相同的MME上，但是MME没有任何UE的上下文。基本的过程如下：
1）UE使用old GUTI给新的MME发送attach request消息。这时候，这个attach request消息是使用NAS安全秘钥（K-NASinc）完整性保护的。
2）因为GUTI包含GUMMEI，新的MME就可以从old GUTI中知道这个旧的GUTI是从哪里分配的。新的MME查找上次UE上下文，但是没有找到任何信息（例如，完整性验证失败或者没有old GUTI）。
3）MME给UE发送identity request消息来请求IMSI。
4）UE给MME发送identity response消息提供请求的IMSI。
5）现在，MME通过使用获取的IMSI按照attach case-1的流程执行鉴权和NAS安全建立过程，接着发送UE位置更新消息给HSS。

**attach case-3：当UE附着到一个之前从来没有附着过的MME上（new MME ！= old MME），且MME没有有效的上次UE上下文信息**

这是当UE在上次去附着之后仍然还保留着上次附着信息，附着到一个新的MME，不是旧的MME，但是就的MME没有和UE关联的有效的UE上下文信息。基本过程如下所示：
1）UE使用old GUTI作为UE ID给新的MME发送attach request消息，这时候这个消息是完整性保护的
2）当新的MME接收到这个消息，他从这个old GUTI知道来自哪里。
3）接着，新的MME向旧的MME发送identification request（old GUTI，完整的attach request消息）把旧的GUTI和attach request消息转发给旧的MME。通过这个消息，新的MME请求和旧的GUTI相关的上次UE上下文。
4）一旦接收到这个消息，旧MME查找UE上下文，但是没有找到任何信息。
5）旧MME给新MME发送identification response消息，通知没有找到UE上下文。

从这儿开始开始和attach case-2相同，执行attach case-2的3/4/5。新MME发送给UE identify request消息来请求IMSI。接着UE携带自己的IMSI使用identify response消息发送给MME。MME接收到IMSI后，MME开始执行鉴权和NAS安全建立和UE位置更新。

## 2.2 已知UE

图3展示了UE发送attach request初始附着到网络，并且MME有用户有效的UE上下文信息的情况。在初始附着时，不像未知UE，所有的已知UE都是使用GUTI，不是IMSI。在图3中，UE和MME中都包含和用户相关的UE上下文信息，并且UE发送的attach request消息是完整性保护的。

![这里写图片描述](http://img.blog.csdn.net/20150917152809534)

**attach case-4：当UE附着到上次附着过的MME上，并且这个MME中包含用户有效的上次UE上下文**

这是当UE仍旧有上次附着信息（old GUTI，NAS安全上下文），附着到它上次附着的MME，MME具有这个UE的有效UE上下文。具体的场景如下：
1）UE使用old GUTI作为自己的UE ID给new MME发送attach request消息。这个attach request消息是使用NAS完整性秘钥K-NASint完整性保护的。
2）这个new MME从old GUTI中知道谁分配的这个ID。然后它查找old GUTI，找到有效的UE上下文（IMSI，MM上下文（NAS安全上下文，UE-AMBR））。
3）MME对attach request消息执行完整性检查
        i)如果完整性检查失败，MME必须使用IMSI来鉴权用户，并执行用户的NAS安全建立过程。
        ii）如果完整性检查通过，MME会略过鉴权和NAS安全建立过程。

**attach case-5：当UE附着到之前没有附着过的MME上（new MME！=old MME），并且old MME具有有效的上次UE上下文**

这是当仍包含上次附着信息，附着到一个新的MME，在旧的MME上具有有效的UE上下文。一个简单过程如下：
1）UE使用old GUTI作为自己的UE ID给new MME发送attach request消息。这个attach request消息是完整性保护的。
2）new MME从接收到的old GUTI上知道是谁分配的（old MME）。
3）new MME向old MME发送identification request（old GUTI，完整的attach request消息），转发old GUTI和attach request消息。通过这样，new MME请求和old GUTI相关联的上次UE上下文信息。
4）一旦接收到这个消息，old MME查找UE上下文，找到和这个UE相关联的IMSI和MM上下文（NAS安全上下文，UE-AMBR）。
5）old MME对attach request消息执行完整性检查
6）old MME把完整性检查的结果通过identification response消息发给new MME
        i)如果完整性检查失败，old MME在这个消息转发错误原因消息。
        ii）如果完整性检查通过，则转发UE上下文（IMSI，old GUTI，MM上下文）。

如果完整性检查失败，情况和attach case-3相同，因此需要执行和attach case-3上相同的IMSI获取，鉴权和NAS安全建立过程。如果检查通过，new MME从old MME接收到IMSI和MM上下文，并略去鉴权和NAS安全建立的过程（类似case-4）。和case-4不同的一点就是因为UE是附着到一个new MME，这个new MME需要和HSS通信更新UE的位置信息。


# III. Simplified Call Flow in Each Case

第二章介绍了不同类型的初始附着。第三章将描述在每种类型的流程，主要关注在功能模块上。图4描述了基于使用不同的UE ID，每一个初始附着过程具有不同的流程。对于known UE的初始附着情况，我们讨论NAS-MAC完整性检查在什么地方完整。在初始附着过程中执行的功能模块包括：

1）UE ID获取
MME获取去UE ID用于用户身份认证和鉴权。UE ID可以是IMSI和old GUTI。IMSI可以通过attach request或者iDentify response消息从UE中获取，而old GUTI可以通过attach request消息从UE中获取。

2）鉴权
如果MME通过attach request消息获取IMSI或者old GUTI作为UE ID，但是这个消息的完整性检查失败了，网络检查是否需要通过执行EPS AKA过程来允许UE附着。通过产生鉴权向量并发送到MME，HSS产生K-ASME（MME基本秘钥），接着和UE之间执行相互鉴权。

3） NAS安全建立
一旦用户鉴权完成，产生了在UE和MME之间用于NAS消息安全传输的NAS安全秘钥。

4）位置更新
MME从HSS下载用户信息，并且HSS更新UE当前位置的信息。MME只有当i)UE使用IMSI作为UE ID时ii）MME没有有效的上次UE上下文信息iii）MME没有关于用户的任何签约信息iv）UE上次从其他MME上去附着，这些情况下MME才会执行位置更新。

5）EPS会话建立
EPS会话和默认承载建立。

![这里写图片描述](http://img.blog.csdn.net/20150917162518218)

## 3.1 使用IMSI初始附着

**attach case-1：unknown UE**

UE使用IMSI请求初始附着，MME从attach  request消息中获取用户的IMSI。

【UE->MME】attach request（IMSI）

在初始附着时UE如果使用IMSI，MME会执行鉴权，NAS安全建立和位置更新，建立EPS会话/默认EPS承载。

## 3.2 使用GUTI初始附着

**attach case-2：unknown UE，MME unchanged**

UE使用old GUTI请求初始附着，但是MME中没有old GUTI。所以MME向UE请求UE ID，并获取IMSI。

【UE->MME】 attach request（old GUTI）
【MME】 no IMSI
【UE<-MME】 identity request（UE ID=IMSI）
【UE->MME】identity response（IMSI）

剩下来的过程和case-1相同。也就是，MME执行鉴权，NAS安全建立，位置更新和建立EPS会话/默认EPS承载。

**attach case-3：unknown UE，MME changed**

UE使用old GUTI请求初始附着。所以，new MME向old MME请求上次UE上下文，但是没有接收到任何有效的。所以MME向UE请求UE ID，获取IMSI。

【UE->new MME】 attach request（old GUTI）
【new MME->old MME】identification request(old GUTI)
【old MME】no IMSI
【new MME<-old MME】identification response（error cause）
【UE<-new MME】identity request （UE ID=IMSI）
【UE->new MME】identify response（IMSI）

剩下的过程和case-1相同。也就是，MME执行鉴权，NAS安全建立，位置更新和建立EPS会话/默认EPS承载。

**attach case-4： known UE， MME unchanged**

UE使用old GUTI请求初始附着，MME有和old GUTI相关的上次UE上下文。所以不需要获取IMSI的步骤。

【UE->MME】 attach request（old GUTI）
【MME】 IMSI，old GUTI，MM上下文

如果在NAS-MAC完整性检查通过，MME可能不执行鉴权，NAS安全建立和位置更新立即建立EPS会话/默认EPS承载。

**attach case-5：known UE，MME changed**

UE使用old GUTI来请求初始附着。所以new MME向old MME请求上次UE上下文信息，并获取UE的IMSI和MM上下文。

【UE->new MME】 attach request（old GUTI）
【new MME->old MME】identification request(old GUTI)
【old MME】IMSI，old GUTI，MM上下文
【new MME<-old MME】identification response（IMSI，old GUTI，MM上下文）

如果old MME的NAS-MAC完整性检查通过，new MME介绍接收到IMSI和MM上下文，就不执行鉴权和NAS安全建立。但是因为MME发生了改变，new MME需要和HSS通信来更新UE位置信息，并建立EPS会话/默认EPS承载。HSS从old MME到new MME更新UE的位置信息，并发送cancel Location消息给old MME来通知UE的MM上下文信息可以从old MME中删除了。

# IV. Closing

到目前为止，我们讨论了初始附着的不同类型，并且不同类型所需要的步骤。在接下来的文档中，我们将关注详细的初始附着过程，并探讨在这些附着过程中在EPS实体中有哪些信息需要设置。


# References
[1] Netmanias Technical Document, “Eleven EMM Cases in an EMM Scenario”, October 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=6002
[2] Netmanias Technical Document, “LTE Security II: NAS and AS Security”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5903
[3] Netmanias Technical Document, “LTE Security I: LTE Security Concept and LTE Authentication”, July
2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=5902
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.
