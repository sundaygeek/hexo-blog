---
title: '5-LTE Security I: LTE Security Concept and LTE Authentication'
date: 2016-04-03 22:06:16
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE

---

原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=5902

# I. Introduction
实际中的无线通信，因为数据是原始的发送和接收的，这些数据可能被接收，并被意想不到的用户非法使用，存在窃听和篡改的风险。用户的位置和移动轨迹也是可以通过追踪用户连接的小区很容易的跟踪。这将会引起隐私侵权。移动通信提供安全特性来保证在空中接口传输的数据避免被篡改，且阻止意想不到的用户非授权的接入来接收数据，并保护用户的隐私。

LTE安全文档描述LTE网络提供的基本安全特征，包括LTE鉴权，NAS安全，AS安全。LTE鉴权是判定试图接入网络的用户是否是合法的签约用户。NAS安全和AS安全是在NAS和AS级别上在LTE空中接口上传输的数据确保能安全地传送。

LTE安全文档包含两个部分，part I II。第一个部分主要介绍LTE安全的概念和LTE鉴权的详细过程，第二部分主要介绍NAS和AS安全建立过程。

第一部分组成如下：在第二章，将介绍这两个文档的范围和概述，第三张介绍LTE鉴权过程，第四章总结LTE鉴权和LTE鉴权相关的秘钥。

# II. LTE Security Concept
## 2.1 LTE安全的范围和概念

图1展示了LTE安全文档的范围和概念。这些文档的将包含以下三个区域。
1、LTE鉴权：在UE和网络之间执行相互的鉴权。
2、NAS安全：在UE和MME之间执行完整性保护验证和NAS信令的加密（加密和解密）
3、AS安全，在UE和eNB之间执行完整性保护验证和RRC信令消息的加密，执行用户数据的加密。
![这里写图片描述](http://img.blog.csdn.net/20150911150356580)

### LTE鉴权
在移动网络中，鉴权是判定试图接入网络的用户是否是合法的签约用户。在多种多样的可用的鉴权过程中，在LTE网络中，在用户和网络之间使用EPS AKA（Authentication and Key Agreement鉴权和秘钥协商）过程来相互鉴权。

EPS AKA过程是由两步组成，第一步，HSS生成EPS鉴权向量（RAND, AUTN, XRES, K-ASME），并把他们发送给MME。第二步，MME选择EPS鉴权向量中的一个，并使用它来双向鉴权，并相互共享这同一个鉴权秘钥-KASME。相互鉴权是网络和UE相关鉴权的过程。在LTE网络，因为当生成鉴权向量时用户服务网络的ID是需要的，所以网络的鉴权是由用户来执行，用户的鉴权是由网络来执行。

ASME（Access Security Management Entity 接入安全管理实体）是一个从HSS接收顶层秘钥的实体，用在接入网络中。在EPS中，MME充当ASME的功能，K-ASME作为一个顶层秘钥在接入网络中使用。MME代表HSS使用K-ASME和UE实施相互鉴权。一旦相互鉴权，UE和MME共享K-ASME作为鉴权秘钥。

为了避免在无线链路上数据的窃听和篡改，K-ASME不是通过E-UTRAN传输给UE。而是，MME传输鉴权向量的部分内容给UE，使用这些内容来鉴权网络，并像HSS那样生成K-ASME。

### NAS安全

NAS安全，是设计用来在无线链路UE和MME之间安全地传输信令信息的，用来执行NAS信令消息的完整性检查和加密。不懂得秘钥用于完整性检查和加密。完整性检查是必须的，而加密是可选的功能。NAS安全秘钥，例如，完整性秘钥（K-NASint）和加密秘钥（K-NASenc）。

### AS安全

AS安全是用来确保在无线链路上UE和eNB之间数据传输的安全。在控制面对RRC信令消息实施完整性检查和加密，在用户面对IP数据包仅执行加密。不同的秘钥用于RRC信令消息的完整性检查/加密和IP数据包的加密。完整性保护是必须的，而加密是可选的功能。
AS安全秘钥，例如，K-RRCint，K-RRCenc和K-UPenc，是由UE和eNB从K-eNB中衍生的，K-RRCint和K-RRCenc是用于控制面数据的完整性检查和加密，K-UPenc用于用户面数据的加密。完整性检查和加密是在PDCP层执行的。

UE可以从K-ASME中衍生出K-eNB，因为K-ASME是不传输到eNB的，而是由MME从K-ASME中衍生出K-eNB，然后传递给eNB。

## 2.2 LTE安全过程的概述

图2展示了LTE安全过程的概述。![这里写图片描述](http://img.blog.csdn.net/20150911190320082)1显示了LTE鉴权过程，![这里写图片描述](http://img.blog.csdn.net/20150911190346937) ![这里写图片描述](http://img.blog.csdn.net/20150911190402290)2和3展示了相应的NAS和AS安全建立过程。下面对每一个过程的简要描述。然后在第三章和第二篇文档中详细介绍每一个过程。

![这里写图片描述](http://img.blog.csdn.net/20150911190607256)

### 1、LTE鉴权
当用户请求接入LTE网络时，在用户和网络之间使用EPS KAK过程来进行相互鉴权。MME一旦接收到了这样的请求，使用用户的IMSI来标识用户，并向HSS请求鉴权向量（AV）。HSS使用EPS AKA算法生成AV，AV={RAND, XRES, AUTN-HSS, K-ASME}，并转发给MME。

在MME存储AV之后，MME从中选择一个，使用这个AV来和UE执行相互鉴权。MME会转发RAND和AUTH-HSS给UE，然后UE使用EPS AKA算法计算出RES，AUTN-UE和K-ASME。UE比较自己的AUTN-UE和从MME接收到的AUTH-HSS来执行网络鉴权。一旦鉴权完成，发送RES给MME，MME比较从HSS接收到的XRES和从UE接收到的RES来执行用户鉴权。如果ＵＥ和网络都鉴权了，则他们就共享了相同的Ｋ－ＡＳＭＥ（尽管Ｋ－ＡＳＭＥ不在UE和MME之间传送）。

### 2、NAS安全

一旦UE和MME相互鉴权完毕并具有相同的秘钥K-ASME，NAS安全过程开始。在这个过程中，当传送NAS信令消息时，NAS安全秘钥从K-ASME中衍生，用于这些NAS消息的安全传送。这个过程包含NAS消息的一个来回（Security Mode Command 和 Security Mode Complete消息），并在MME传送Security Mode Command 消息给UE是开始。

第一，MME选择一个NAS安全算法（Alg-ID：算法ID），并使用它们来从K-ASME来产生K-NASinc和K-NASenc。接着MME把K-NASinc算法用于Security Mode Command消息产生一个NAS消息鉴权码（NAS-MAC：Message Authentication Code for NAS for Integrity)。MME接着传输包含选择的NAS安全算法和NAS-MAC的Security Mode Command消息给UE。因为UE并不知道选择的加密算法，所以这个消息是完整性保护的，但是没有加密。

一旦接收到了Security Mode Command消息，UE使用MME选择的NAS完整性算法来验证完整性，并使用NAS完整性/加密算法从K-ASME中产生NAS安全秘钥（K-NASinc，K-NASenc）。接着使用K-NASenc加密Security Command Complete消息，并使用K-NASinc生成消息鉴权码NAS-MAC用于加密的消息。现在UE可以传输包含NAS-MAC的加密和完整性保护的消息到MME了。

一旦NAS安全建立起来，在UE和MME之间的NAS信令都是通过NAS安全秘钥加密和完整性保护的，在无线链路上安全的传输。

### 3、AS 安全

在NAS安全建立完成之后，在UE和eNB之间AS安全建立过程开始。在这个过程中，当传输RRC信令消息和IP数据包时，使用从K-eNB产生的AS安全秘钥用于这些数据的安全传输。这个过程包括RRC信令消息的一个来回（Security Mode Command 和 Security Mode Complete 消息），并且这个过程在eNB传输Security Mode Command消息给UE时开始。

第一，MME从K-ASME中计算出K-eNB并传输给eNB，eNB使用K-eNB来执行AS安全过程。eNB选择AS安全算法（Alg-ID：算法ID）并使用这个算法ID从K-eNB中计算出完整性秘钥（K-RRCinc）和加密秘钥（K-RRCenc）用于RRC信令消息，计算出加密秘钥（K-UPenc）用于用户面。接着，应用K-RRCint到Security Mode Command消息产生一个消息鉴权码（MAC-I，Message Authentication Code for Integrity）。现在eNB开始传输包含选择的AS安全算法和MAC-I的Security Mode Command消息到UE。

一旦从eNB接收到Security Mode Command消息，UE使用eNB选择的AS完整性算法验证完整性，并使用AS完整性/加密算法来计算出AS加密秘钥 (K-RRCint, K-RRCenc and K-UPenc)。接着UE使用RRC完整性秘钥产生一个消息鉴权码MAC-I给Security Mode Complete 消息，接着转发包含MAC-I的这条消息给eNB。

当eNB使用AS安全秘钥成功的验证了接收到的Security Mode Complete 消息的完整性，AS安全建立过程完成的。

在AS安全建立之后，UE和eNB之间的RRC信令消息都是使用AS安全秘钥加密的和完整性保护的，在空中链路上传输的用户IP数据包是加密的。

# III. LTE Authentication Procedure
在第二章简要介绍了LTE鉴权过程，接下来将详细介绍。图3是当UE附着到LTE网络时执行基于EPS AKA的LTE鉴权过程。在USIM和HSS/AuC上存储着固定的秘钥（LTE 秘钥K，IMSI）。当UE被生产的时候，在USIM卡上就存储了LTE K和IMSI，并且当用户开始签约LTE网络时在HSS/AuC中提供。

![这里写图片描述](http://img.blog.csdn.net/20150912084712477)

## 3.1 由UE请求的鉴权

1、[UE -> MME] UE请求对网络的鉴权

当UE在初始附着时试图接入网络，UE传送Attach Request消息给MME，这个消息触发了EPS AKA过程。在Attach Request消息中包含(IMSI, UE网络能力, KSIA-SME=7)参数。
其中

 - IMSI：（ International Mobile Subscriber Identity），是和用户关联的唯一的ID。
 - UE网络能力：UE可用的安全算法。
 - KSIA-SME=7：指示UE没有安全秘钥。

UE网络能力通知MME 连接到EPS的UE具有什么样的能力，并指示UE支持的NAS和AS算法，比如EEA和EIA（EPS Encryption Algorithms 和 EPS Integrity Algorithms）。他们是用1bit的值来标识是否存在，（比如EEA0=0，EEA1=1......EIA1=1，EIA2=1....），表1列出了UE网络能力信息。

![这里写图片描述](http://img.blog.csdn.net/20150912085939485)
KSI-ASME标识UE和MME中的K-ASME，由3bit组成，从000到111（0-7），7是标识在UE中没有可用的K-ASME。

## 3.2 在MME和HSS之间鉴权数据的交换

2、[MME -> HSS]MME向HSS请求鉴权数据

MME识别到UE没有可用的K-ASME，通过向HSS发送Authentication Information Request消息来发起LTE鉴权过程获取新的鉴权数据。Authentication Information Request消息包含(IMSI, SN ID, n, Network Type)参数。
其中

 - IMSI：和用户关联的唯一的ID。
 - SN ID（Serving Network ID服务网络ID）：是指用户接入的网络，由PLMN ID组成。
 - n（鉴权向量的个数）：MME请求鉴权向量的个数
 - Network Type：UE接入网络的类型（在此是E-UTRAN）。

一旦从MME接收到Authentication Information Request消息，HSS生成RAND和SQN，并使用EPS AKA算法用LTE K，SQN，RAND计算出XRES，AUTN，CK，IK。然后使用KDF（Key Derivation Function秘钥派生函数）用CK，IK，SQN和SN ID（PLMN）计算出接入网络的顶级秘钥（K-ASME），然后传输给MME。KDF是一个单向函数。因为在生成K-ASME时需要SN ID，所以当服务网络发生变化时K-ASME需要重新生成。在K-ASME生成后，HSS形成鉴权向量组AV-i=（RAND-i，AUTN-i，XRES-i，K-ASME-i），i=0....n-1.

3、[MME <- HSS]HSS响应鉴权数据的请求

HSS应MME请求生成需要AV，然后发送Authentication Information Answer (AVs)消息给MME。

## 3.3 UE和MME之间的相互鉴权

MME存储从HSS接收到的AV，然后在UE的LTE鉴权中选择其中一个使用。在图3中，MME选择第i个AV。K-ASME是MME基本的秘钥，在接入网络中作为一个顶级的秘钥起作用。它只存在于EPC中，不会通过E-UTRAN传送给UE，因为那样传输是不安全的。MME分配KSI-ASME（K-ASME的索引）代替K-ASME传送给ＵＥ，因此UE和MME都能使用KSI-ASME来代替K-ASME（在图3中，KSI-ASME=1）。

4、[UE<-MME]MME请求对用户鉴权
MME保留在AV中的K-ASME-i和XRES-i，把KSI-ASME-i代替K-ASME-i，RAND-i，AUTN-i包含在Authentication Request消息中传递给ＵＥ。XRES-i会在待会鉴权用户的时候用到。

UE一旦从MME接收到Authentication Request消息，传递RAND-i和AUTN-i给USIM，USIM使用和HSS相同的EPS AKA算法用存储的LTE K和接收到的RAND-i和HSS生成的SQN（SQN隐藏在AUTN中）计算出RES，AUTN-UE，CK，IK。UE比较通过EPS AKA算法产生AUTN-UE和从MME接收到的AUTN来鉴权LTE网络。

5、[UE->MME]UE响应对用户的鉴权
一旦UE完成网络鉴权，UE发送包含由EPS AKA算法生成RES的Authentication Response消息给MME。如果使用AUTN网络鉴权失败，UE包含失败原因的Authentication Failure消息给MME。

当MME接收到从UE的Authentication Response消息，将对比由UE生成的RES和从HSS接收到的AV中的XRES-i来鉴权用户。

在完成网络鉴权之后，USIM卡传输CK，IK给UE。UE使用KDF函数用CK，IK，SQN，SN ID生成K-ASME，并使用从MME接收到的KSI-ASME作为它的索引存储。然后，在UE和MME之间NAS安全监理时用KSI-ASME代替K-ASME。


# IV. Closing

上面，我们已经讨论了LTE鉴权过程。LTE鉴权是基于EPS AKA过程的用户和网络之间的相互鉴权。在服务网络中的MME和UE代替HSS来执行相互鉴权，鉴权的结果是在UE和MME之间具有相同的K-ASME。在表2中总结了LTE鉴权相关的秘钥。在第二个文档将基于K-ASME来介绍NAS和AS安全。
![这里写图片描述](http://img.blog.csdn.net/20150912102644835)

# References
[1] Netmanias Technical Document, “LTE Security II: NAS and AS Security”, August 2013,
http://www.netmanias.com/en/?m=view&id=techdocs&no=5903.
[2] 3GPP TS 24.301, “Non-Access-Stratum (NAS) protocol for Evolved Packet System (EPS); Stage 3”.
[3] 3GPP TS 33.401, “3GPP System Architecture Evolution (SAE); Security architecture”.
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.


