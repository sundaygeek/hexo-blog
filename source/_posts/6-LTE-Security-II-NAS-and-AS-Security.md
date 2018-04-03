---
title: '6-LTE Security II: NAS and AS Security'
date: 2018-04-03 22:18:13
tags:
    - LTE
    - 通信
categories:
    - 通信
    - LTE
---

原文链接：http://www.netmanias.com/en/?m=view&id=techdocs&no=5903

# I. Introduction

在LTE安全的第一个文档，我们介绍了基于EPS AKA过程的LTE鉴权，并在鉴权结束后，UE和MME之间共享了相同的K-ASME。在这个文档中，我们将基于K-ASME来介绍NAS和AS安全，学习在安全建立过程后用户和控制面的数据是怎样传输的。

第二章将介绍NAS安全建立过程，和在这个过程后NAS消息时怎么发送和接收的。第三章介绍AS安全建立过程，并介绍RRC和IP数据包时怎么传输的。第四章在EPS实体上使用的EPS安全上下文和安全数据。最后第五章总结在两篇LTE安全文档中的所有安全秘钥。

在我们开始介绍安全建立过程之前，我们先看下NAS和AS所有应用的协议栈。图1表示NAS和AS安全建立相关的协议栈。

![这里写图片描述](http://img.blog.csdn.net/20150912103511820)

 - NAS安全：NAS安全的目的是为了确保UE和MME之间控制面NAS消息是使用NAS安全秘钥安全传送的。NAS安全秘钥是从K-ASME中计算出来，并且每次执行EPS AKA都会有一个新的秘钥。在NAS安全建立完成之后，UE和MME之间在传输之前共享了相同的NAS安全秘钥（K-NASenc）和NAS完整性秘钥（K-NASinc）分别用于加密和完整性保护。
 - AS安全：AS安全的目的是为了确保UE和eNB之间控制面RRC消息和用户面IP数据包是使用AS安全秘钥安全传送的。AS安全秘钥是从K-eNB中计算出来的，并且每次一个新的无线链路建立后都会产生一个新的秘钥。在AS安全建立完成后，UE和eNB之间共享了RRC完整性秘钥（K-RRCinc）、RRC加密秘钥（K-RRCenc）和用户加密秘钥（K-UPenc）。使用这些秘钥的加密和完整性包保护都是在PDCP层执行的。RRC完整性秘钥（K-RRCinc）、RRC加密秘钥（K-RRCenc）都用来确保在无线链路控制面上通过SRB传输的RRC消息的传输安全。RRC消息在发送之前在PDCP层使用K-RRCinc完整性保护和使用K-RRCenc加密。K-UPenc用于在无线链路上用户面通过DRB传输的IP数据包的安全传输。IP数据包在传输之前在PDCP层使用K-UPenc来加密。

# II. NAS Security

下面将对NAS安全展开详细描述。NAS安全建立过程包含在UE和MME之间的Security Mode Command消息（MME发送到UE的）和Security Mode complete消息（UE发送给MME的）。这个NAS安全过程和NAS消息怎么传输在2.1和2.2节分别阐述。

## 2.1 NAS安全建立

（1）、Security Mode Command消息的传输
图2展示了在NAS安全建立过程中Security Mode Command消息时怎么传送的。MME通过发送Security Mode Command消息给UE来通知UE，MME是被网络鉴权的，并且表示用于消息安全传输的NAS安全建立过程已经发起。Security Mode Command消息时完整性保护的，接着发送给ＵＥ，然后ＵE计算出NAS安全秘钥（加密秘钥和完整性秘钥）并使用完整性秘钥来验证这个消息的完整性。

NAS安全建立过程之前的LTE鉴权过程在图2中![这里写图片描述](http://img.blog.csdn.net/20150912110350911) ![这里写图片描述](http://img.blog.csdn.net/20150912110400600)显示。LTE鉴权的结果是在UE和MME之间共享了K-ASME。我们开始介绍NAS安全建立过程，假定MME分配KSI-ASME=1来标识K-ASME。

![这里写图片描述](http://img.blog.csdn.net/20150912110618993)

1、[MME]选择安全算法
MME在从UE接收到的Attach Request消息中基于网络能力信息选择加密和完整性算法用于NAs消息。图2展示了选择EEA1作为加密算法，EIA1作为完整性算法。

2、[MME]计算出NAS安全秘钥
MME使用算法ID（Alg-ID）和选择安全算法的算法分辨器来从K-ASME中计算出K-NASinc和K-NASenc。表1列出了算法ID和算法分辨器。
![这里写图片描述](http://img.blog.csdn.net/20150912111105268)

3、[MME]为完整性保护产生NAS-MAC
MME生成Security Mode Command消息发送给ＵＥ，并使用ＥＩＡ算法和输入参数计算出NAS-MAC。图3是展示了NAS-MAC是怎么生成的。
参数说明：

 - Count：32bit下行NAS计数
 - Message：NAS消息，在这里是Security Mode Command
 - Direction：1bit表示传输的方向，0是上行，1是下行
 - Bearer：5bit的承载ID，固定值，设置为0
 - K-NASinc：128bit的NAS完整性秘钥

![这里写图片描述](http://img.blog.csdn.net/20150912111735627)

4、[UE<-MME]发送Security mode command消息
MME把计算出来的NAs-MAC包裹在Security mode command消息中发送给ＵＥ。这个消息时完整性保护而没有加密的。
消息参数有以下几个：

 - KSI-ASME：3bit和K-ASME关联的值，用来表示K-ASME
 - 重返UE安全能力：UE安全能力包裹在UE发送的attach Request消息中的UE网络能力，指示UE可以支持的安全算法。
 - NAS加密算法：由MME选择的NAS加密算法，这里为EEA1
 - NAS完整性算法：由MME选择的NAS完整性算法，这里为EIA1

5、[UE]设置K-ASME表示（KSI-ASME）
当UE从MME接收到Security mode command消息，UE设置在消息中的KSI-ASME，并使用它来表示当前的K-ASME。

6、[UE]产生NAS安全秘钥
UE识别出MME选择的NAS安全算法，并使用算法ID和算法分辨器从K-ASME中计算出K-NASinc和K-NASenc。

7、[UE]检查Security mode command消息的完整性
UE通过使用包裹在消息中的NAS-MAC来检查Security mode command消息的完整性。UE识别出由MME选择的NAS完整性算法是EIA1，并使用Security mode command消息中选择的EIA1算法计算出XNAS-MAC消息鉴权码和K-NASinc。图4是介绍XNAS-MAC是怎么通过相同的EIA计算出来的。UE通过比较由自己计算出来的XNAS-MAC和由MME计算出来NAS-MAC是否匹配来验证这个消息的完整性。如果匹配，它表示Security mode command消息是没有在途中被篡改的。

![这里写图片描述](http://img.blog.csdn.net/20150912113734165)

（2）、Security mode complete消息的传输
图5展示了Security mode complete消息在NAS安全建立过程中是怎么传输的。UE通过发送Security mode complete消息给MME来通知MME在UE中生成了和MME中相同的NAS安全秘钥，并且Security mode command消息完整性验证通过。Security mode complete消息传输是加密和完整性保护的。

![这里写图片描述](http://img.blog.csdn.net/20150912123549147)

8、[UE]使用选择的加密算法EEA1加密消息
UE生成和加密Security mode complete消息发送给MME。Security mode complete消息的加密数据流（Cipher Text Block加密数据块）是通过Security mode complete消息（Plane Text Block面数据块）和由EEA1、K-NASenc生成的加密秘钥流（Key Stream Block秘钥流块）经过bit位执行XOR异或操作来生成的。图6展示了NA消息时怎么加密的。用来产生秘钥流块的EEA算法的输入数据包括以下：

 - Count：32bit上行NAS计数
 - Bearer：5bit的承载ID，固定值，设置为0
 - Direction：1bit表示传输的方向，0是上行，1是下行
 - Length：通过加密算法陈胜的秘钥流的长度
 - K-NASenc：128bit的NAS加密秘钥

![这里写图片描述](http://img.blog.csdn.net/20150912124429617)

9、[UE]产生NAS-MAC用于完整性保护
UE使用EIA算法计算出NAS-MAC和K-NASint。图3a是NAS-MAC是怎么使用下面的EIA参数生成的。

 - Count：32bit上行NAS计数
 - Message：NAS消息，在这里是Security Mode complete
 - Direction：1bit表示传输的方向，0是上行，1是下行
 - Bearer：5bit的承载ID，固定值，设置为0
 - K-NASinc：128bit的NAS完整性秘钥

![这里写图片描述](http://img.blog.csdn.net/20150912124841854)

10、[UE->MME]发送Security Mode complete消息
UE把计算得到的NAS-MAC包裹在Security Mode complete消息中，并发送给MME。在这里这个消息是加密和完整性保护的，在这个之后，所有的UE发送给MME的NAS消息都是安全传输的。

11、[MME]验证Security Mode complete消息的完整性
MME通过验证包含在消息中的NAS-MAC来检查Security Mode complete的完整性。MME使用在Security Mode complete消息中选择的EIA1算法和K-NASint来计算出XNAS-MAC。图4a展示了XNAS-MAC是怎么使用相同的EIA参数来计算出来的。MME通过检查自己计算出来的XNAS-MAC和从UE计算出来的NAS-MAC是否匹配来验证这个消息的完整性。如果匹配，它表示Security Mode complete消息在传输过程中没有被篡改。

![这里写图片描述](http://img.blog.csdn.net/20150912125520560)

12、[MME]解密Security Mode complete消息
在成功验证Security Mode complete消息之后，MME开始使用EEA算法解密这个消息。Security Mode complete消息（UE产生的原来消息）是通过加密的Security Mode complete消息和秘钥流块XOR异或产生的。图7表示这个消息怎么使用EEA算法被解密的。

![这里写图片描述](http://img.blog.csdn.net/20150912125831997)

## 2.2 NAS安全建立后

一旦NAS安全建立完成，在随后所有的UE和MME之间传输的NAS消息都是在发送之前加密和完整性保护的。图8显示了在NAS安全建立之后，ＵＥ和MME之间的NAS消息时怎么传送的。

![这里写图片描述](http://img.blog.csdn.net/20150912130134923)

当NAS消息发送之前，首先加密然后完整性保护。原NAS消息首先使用K-NASenc加密然后包含K-NASint计算出来的NAS-MAC做完整性保护，所有这些消息都是加密和完整性保护传送的。

当接收到NAS消息，首先做完整性验证，然后解密，和发送时相反的过程。首先对比使用K-NASint计算出来的XNAS-MAC和接收到的NAS-MAC对比来检查NAS消息的完整性，接着解密得到原始的NAS消息。

![这里写图片描述](http://img.blog.csdn.net/20150912130647607)

# III. AS Security

下面详细介绍AS安全。AS安全建立过程包含在UE和MME之间的Security Mode Command消息（MME发送到UE的）和Security Mode complete消息（UE发送给MME的）。通过RRC信令来AS安全建立过程的描述和RRC消息在控制面、IP数据包在用户面怎么传输在3.1和3.2节分别阐述。

## 3.1 AS安全建立

（1）、Security mode command消息的传送
图9和图10描述了在AS建立过程中Security mode command消息是怎样传输的。首先，在图9中显示eNB怎样生成AS安全秘钥，并传输Security mode command消息给UE。K-eNB（AS安全基本秘钥）是从K-ASME计算出的，然后eNB从K-eNB中计算出AS安全秘钥。因为K-ASME不会传送到eNB，所以MME从K-ASME计算出K-eNB，然后传输给eNB，接着从传递的K-eNB中计算出AS安全秘钥。
1和2展示了LTE鉴权过程。

![这里写图片描述](http://img.blog.csdn.net/20150912131606012)

1、【MME】计算出K-eNB
MME使用秘钥生成函数KDF用K-ASME和UL NAS Count计算出K-eNB。

2、【eNB<-MME】发送K-eNB
MME作为对attach Request的响应发送attach accept消息给UE。这个NAS消息时通过initial context setup Request消息（这是一个eNB和MME之间的S1信令消息）传输的。消息的参数如下：

 - UE安全能力：MME从UE发送的attach Request中的UE网络能力中选择的安全算法。
 - Security key：256bit K-eNB

3、【eNB】选择安全算法
eNB根据从MME接收到的initial context setup Request消息中包含的UE安全能力选择加密和完整性保护算法应用于RRc消息和IP数据包。图9展示了选择EEA1作为加密算法，选择EIA1作为完整性保护算法。

4、【eNB】产生AS安全秘钥
eNB使用算法ID和选择的安全算法的算法分辨器从K-eNB中衍生出K-RRCint、K-RRCenc、K-UPenc。
![这里写图片描述](http://img.blog.csdn.net/20150912132816872)

5、【eNB】为完整性保护产生MAC-I
eNB生成Security Mode Command消息发送给ＵＥ，并使用ＥＩＡ算法和K-RRCint计算出MAC-I。图3是展示了MAC-I是怎么生成的。
参数说明：

 - Count：32bit下行PDCP计数
 - Message：RRC消息，在这里是Security Mode Command
 - Direction：1bit表示传输的方向，0是上行，1是下行
 - Bearer：5bit的承载ID
 - K-RRCinc：128bit的NAS完整性秘钥（此处原文有误）

6、[UE<-eNB]发送Security mode command消息
eNB把计算出来的MAC-I包裹在Security mode command消息中发送给ＵＥ。这个消息时完整性保护而没有加密的。
消息参数有以下几个：

 - AS加密算法：由eNB选择的AS加密算法，这里为EEA1
 - AS完整性算法：由eNB选择的AS完整性算法，这里为EIA1

图10展示了UE怎么从Security mode command消息中获得AS秘钥并用于消息的完整性检查。

![这里写图片描述](http://img.blog.csdn.net/20150912133540763)

7、[UE]识别安全算法：EEA1、EIA1
UE从接收到的Security mode command消息中识别出eNB选择的AS安全算法和完整性保护算法，图10是选择EEA1和EIA1的实例。

8、[UE]产生AS安全秘钥
UE并使用算法ID和算法分辨器从K-eNB中计算出K-RRCinc和K-RRCenc和K-UPenc。

9、[UE]检查Security mode command消息的完整性
UE通过使用K-RRCint来验证包裹在消息中的MAC-I来检查Security mode command消息的完整性。UE通过比较由自己计算出来的XMAC-I和由eNB计算出来MAC-I是否匹配来验证这个消息的完整性。如果匹配，它表示Security mode command消息是没有在途中被篡改的。图4是XMAC-I计算的过程。

（2）、Security mode complete消息的传输
图11展示了Security mode complete消息在AS安全建立过程中是怎么传输的。UE通过发送Security mode complete消息给MME来通知MME在UE中生成了和MME中相同的AS安全秘钥，并且Security mode command消息完整性验证通过。Security mode complete消息传输是加密和完整性保护的。

![这里写图片描述](http://img.blog.csdn.net/20150912134308354)

10、[UE]产生NAS-MAC用于完整性保护
UE使用EIA算法计算出MAC-I和K-RRCint。图3是MAC-I是怎么使用下面的EIA参数生成的

11、[UE->MME]发送Security Mode complete消息
UE把计算得到的MAC-I包裹在Security Mode complete消息中，并发送给eNB。在这里这个消息是加密和完整性保护的。

12、[MME]验证Security Mode complete消息的完整性
MME通过验证包含在消息中的MAC-I来检查Security Mode complete的完整性。eNB使用在Security Mode complete消息中选择的EIA1算法和K-RRCint来计算出XMAC-I。eNB通过检查自己计算出来的XMAC-I和从UE计算出来的MAC-I是否匹配来验证这个消息的完整性。如果匹配，它表示Security Mode complete消息在传输过程中没有被篡改。

## 3.2 AS安全建立后

一旦AS安全建立完成，在随后所有的UE和eNB之间传输的RRC消息都是在发送之前加密和完整性保护的，所有的IP数据包也是加密的。图12显示了在AS安全建立之后，ＵＥ和eNB之间的RRC消息和IP数据包是怎么传送的。

![这里写图片描述](http://img.blog.csdn.net/20150912134908008)

当RRC消息发送之前，不像NAS消息，它会首先完整性保护然后加密发送。原NAS消息首先包含K-RRCint计算出来的MAC-I做完整性保护接着使用K-RRCenc加密，所有这些消息都是加密和完整性保护传送的。

当接收到RRC消息，首先做解密，然后完整性验证，和发送时相反的过程。首先使用K-RRCenc解密获得完整性保护的RRC消息，然后对比使用K-NASint计算出来的XNAS-MAC和接收到的NAS-MAC对比来检查NAS消息的完整性，确认得到原始的NAS消息。

![这里写图片描述](http://img.blog.csdn.net/20150912135318659)

用户面数据是加密的但是不进行完整性保护。用户数据包在发送端使用K-UPenc进行加密，在接收端使用K-UPenc进行解密获得原始的用户数据包。

# IV. Security Context

到目前为止，我们已经讨论完了LTE鉴权过程，NAS安全和AS安全过程。在这些过程中和安全相关的数据会在EPS实体中设置，这些数据叫EPS安全上下文，可以使NAS安全上文也可以是AS安全上下文。NAS安全上下文可以有两种类型："full native" or "partial native"。在EPS AKA执行之后，第一个SMC进行之前的NAS安全上下文叫"partial native"。一个"partial native"的EPS NAS安全上下文在SMC过程完成之后转变为"full native" 。表2列出了这些EPS安全上下文。
![这里写图片描述](http://img.blog.csdn.net/20150912140011568)

图13显示了在EPS AKA和NAS/AS安全建立之后存储在EPS实体中的LTE安全数据。它显示了这些每一个安全数据是如何生成的，并且数据传输流指示从哪个数据开始安全数据开始传输。

![这里写图片描述](http://img.blog.csdn.net/20150912140337600)

# V. Closing

LTE安全文档已经覆盖包括基于EPS AKA的LTE鉴权，和NAS、AS安全建立过程、在EPS实体中安全数据等的LTE安全技术。我们还学会了LTE安全秘钥的层次结构，他们是分离的，用于不同的目的。顶级的秘钥是K（LTE秘钥），它是一个固定的值存储在USIM和HSS/AuC中。从K，衍生出了CK、IK，然后从CK、IK衍生出K-ASME。NAS秘钥和K-eNB从K-ASME中衍生。从K-eNB又衍生出了AS安全秘钥。我们还发现从UE、eNB、MME衍生的秘钥取决于他们是用于NAS层还是AS，是用于控制面还是用户面，是用于加密还是完整性保护，或者是使用哪一个种算法。表3列出了所有的LTE安全秘钥。

![这里写图片描述](http://img.blog.csdn.net/20150912141013742)

# References
[1] Netmanias Technical Document, “LTE Security I: LTE Security Concept and LTE Authentication”, July
2013, http://www.netmanias.com/en/?m=view&id=techdocs&no=5902
[2] 3GPP TS 33.401, “3GPP System Architecture Evolution (SAE); Security architecture”.
[3] 3GPP TS 24.301, “Non-Access-Stratum (NAS) protocol for Evolved Packet System (EPS); Stage 3”.
[4] NMC Consulting Group Confidential Internal Report, “E2E LTE Network Design”, August 2010.

