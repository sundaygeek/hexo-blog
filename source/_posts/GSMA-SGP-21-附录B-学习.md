---
title: GSMA SGP.21 附录B 学习
date: 2018-05-12 15:58:33
tags:
- GSMA
- SGP21
- eSIM
---

附录Bprofile生产程序（资料性附录）
## B.1profile生产过程
本节描述了一个通用实施.它应该被视为一个例子而已;可能需要特定的实施来解决特定的安全问题.在eUICC中，UICC的当前功能由一个profile表示.与当前的UICC一样，profile是运营商的责任，profile制作是根据他们的请求和许可（如果不是由运营商自己制作）执行的.应用与当前UICC相同的操作员程序.

Snipaste_2018-05-12_16-06-49.png

profile生成由三个步骤组成：
profile描述定义：SM-DP +基于操作profile描述创建并注册profile描述. 
操作员证书生成：操作员要求SM-DP +生成将在下一步中使用的操作员证书.此过程是可选的，如果操作员想要在生成受保护的profile包期间生成操作员证书，则不会使用该过程. 
受保护的profile包生成：profile包将被创建，保护和存储.此步骤（批次类型的操作或实时过程）仅在相应操作员下单后执行. 
合同结论和链接profile：在合同结束时，将激活码发送给最终用户，并可为该合同分配profile.注意：绑定profile包的生成是profile下载的一部分，使用第5.2.2节中的激活码过程. 

### B.1.1profile描述定义
profile描述定义可以包含以下序列：
Snipaste_2018-05-12_16-07-52.png

开始条件：
a.运营商与SM-DP +之间的契约关系.

步骤：
1.运营商定义其不同的profile类型（由[非标准化]profile描述ID标识），其中包含网络访问应用程序，如USIM，文件结构，数据和应用程序等.
2. SM-DP +创建profile描述基于操作员输入和相应的profile描述ID. 
3. SM-DP +确认profile描述定义，例如通过发送相应的profile描述ID.注意：操作员可以使用SM-DP +

结束条件定义多个profile描述：a.操作员可以根据profile描述ID来订购受保护的profile包. 

### B.1.2生成操作员证书此过程
允许操作员在SM-DP +上分配一组操作员证书，而不将它们关联到特定的ProfileDescriptionID.操作员证书生成可以包含以下序列：
Snipaste_2018-05-12_16-08-02.png

开始条件：
a.运营商已经分配了IMSI，ICCID和其他资源.

步骤：
3.运营商提供IMSI，ICCID，要创建的证书类型（例如Milenage [11]、[12]，TUAK [10]等）以及可能已分配给SM-DP +的其他资源.它要求SM-DP +安全地生成并存储一组操作员证书. 
4. SM-DP +根据运营商的输入以及相应的IMSI，ICCID和其他资源安全地生成并存储一组运营商凭证. 
5. SM-DP +确认操作员证书的生成并将其提供给操作员. 

### B.1.3受保护的profile包生成
受保护的profile包生成可以包含以下序列：此过程可以应用于profile描述定义，合同结论和链接profile之间，具体取决于受保护的profile包是按需创建还是准备好提前.
Snipaste_2018-05-12_16-08-12.png

开始条件：
a.profile描述定义

过程：
1.操作员通过向SM-DP +提供profile描述ID和一些相应的操作员输入数据（证书，例如ICCID，IMSI）来生成受保护的profile包生成.操作员输入保护profile包生成所需的数据（由IMSI，ICCID，K / Ki，OTA密钥，PIN，PUK等）由操作员创建（并提供给SM-DP +）或由SM-DP +（并提供给操作员）. 
2. SM-DP +创建profile包. 
3. SM-DP +创建受保护的profile包. 
4. SM-DP +存储受保护的profile包（安全地）. 
5. SM-DP +确认受保护的profile包生成，并最终发送由SM-DP +创建的附加操作员输入数据. 
6.运营商在运营商系统（如HLR / AuC和BSS）中注册运营商数据.

结束条件：
a.订购的受保护的profile包可在SM-DP +上找到.运营商能够激活这些订阅，并且可以在绑定到EID时触发profile下载.

### B.1.4合同结论和链接profile
激活码必须提供给最终用户才能实现profile下载过程.合同结论和链接profile过程描述了将合同与激活码流程链接在一起的不同场景.

以下选项描述如下：
具有已知EID的激活码：EID由订户在合同签订期间给予运营商. 
具有未知EID的激活码：订户在合同签订期间未向EID提供EID. EID只在profile下载过程中提供给SM-DP +，并从SM-DP +返回给运营商. 
提供给运营商的EID激活码：订户在合同结束时不立即给出EID，但在步骤2中向运营商提供.

合同参考可以是但不一定是任何激活码参数（例如令牌），ICCID或IMSI.在任何情况下，SM-DP +都应能够在profile下载过程中分配和链接profile到相应的eUICC.

#### B.1.4.1 具有已知EID的激活码
Snipaste_2018-05-12_16-15-52.png

程序：
图31中的步骤1-11：已知EID的合同结论
1.订户与运营商签订合同，并在此过程中提供EID. 
2.至5.或者'在profile下载程序之前由运营商分配ICCID：运营商分配profile并将EID，IMSI和ICCID发送到SM-DP +. SM-DP +链接不同的参数并将其确认给操作员.
6.至10.或者'在profile下载程序之前通过SM-DP +进行ICCID分配'：操作员将EID，IMSI和profile描述ID发送到SM-DP +. SM-DP +将ICCID分配给相应的profile，链接不同的参数并确认分配的ICCID和与运营商的链接. 11.运营商向订户确认合同结论以及相应的信息（合同参考）.

结束条件：
a.认购人已与运营商签订合同和有效认购.
b.通知SM-DP +未来的profile下载过程请求. 

#### B.1.4.2具有未知EID的激活码
Snipaste_2018-05-12_16-16-06.png

过程：
图32中的步骤1-6：没有EID的合同结论
1.订户与运营商签订合同，但不知道目标eUICC（EID）. 
2.或者'运营商分配ICCID'：运营商分配profile（ICCID）
3至5.或者'通过SM-DP +分配ICCID'：运营商将profile模板（ID）发送至SM-DP +. SM-DP +分配相应的profile（ICCID）并将分配的ICCID发送给运营商. 
6.运营商向订户确认合同结论以及相应信息（合同参考）.

结束条件：a.认购人已与运营商签订合同和有效认购. 

#### B.1.4.3 EID提供给操作员的激活码
Snipaste_2018-05-12_16-16-17.png

步骤：图33中的步骤1-11：向运营商提供EID的激活码
1.在不知道目标eUICC（EID）的情况下，用户与运营商签订合同. 
2.或者'运营商分配ICCID'：运营商分配profile（ICCID）
3至5.或者'通过SM-DP +分配ICCID'：运营商将profile模板（ID）发送至SM-DP +. SM-DP +分配相应的profile（ICCID）并将分配的ICCID发送给运营商. 
6.运营商向订户确认合同结论以及相应信息（合同参考）. 
7.订户选择设备/ eUICC后，EID将与合同参考一起提供给运营商. 
8.至10.运营商请求SM-DP +链接eUICC（EID）和Profile（ICCID）. SM-DP +链接EID和ICCID并向运营商确认. 
11.运营商确认EID与相应的订户合同的链接.

结束条件：
a.认购人已与运营商签订合同和有效认购.
b.通知SM-DP +未来的profile下载过程请求.

