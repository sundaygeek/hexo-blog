---
title: GSMA SGP.01 第1/2章学习
date: 2018-05-12 16:41:36
tags:
- GSMA
- SGP01
- eSIM
---

# 1简介
## 1.1概述
许多机器对机器设备将不易于达到供应订购的目的。这将需要一个新的解决方案来适应这种特殊情况。要求是在假定采用与今天相同或相似的认证协议的情况下，为具有获得移动网络访问所必需的凭证的“无线”远程配置机器对机器设备定义机制。 MNO必须能够响应请求，将订阅（合同）从一个MNO A更改为不同的MNO B，而无需物理访问相关设备中的嵌入式UICC。本文档描述了一种体系结构，该体系结构在实施时将启用远程供应和订阅管理，同时至少为网络运营商和客户保持与现有解决方案相同的安全级别。这包括MNO网络访问凭证的安全保存，例如加密功能的密钥，以及IMSI和其他客户身份标识符等标识符的使用。 
## 1.2问题范围
本文件涉及：
•GSMA“嵌入式SIM任务组要求和用例”版本1.0 [1]中描述的机器对机器用例。该解决方案不适用于传统的消费电信设备，因为它们不关心上述问题陈述。 
•嵌入式UICC远程供应系统的体系结构，即其组件和相关接口。 GSMA的嵌入式UICC生态系统文件[2]以及第2节中所述的原则和假设将支持其定义。 
•嵌入式UICC的远程供应系统的安全性。 
•网络基础设施中的SM-SR和SM-DP集成选项。 
•嵌入式UICC架构及其外部接口的必要方面，以确保在交付ETSI标准之前与GSMA架构兼容。 
•在适当的地方标准化嵌入式UICC远程供应架构。

## 1.3文档目的
本文档的目的是定义一个通用的全局体系结构框架，以实现机器设备中嵌入式UICC（eUICC）的远程供应和管理，这些设备不易到达。采用通用架构框架将为确保潜在的不同MNO部署方案之间的全球互用性提供基础，同时利用标准化的eUICC平台。本文档确定了各个角色和潜在的角色以及架构中每个角色之间的界面。 

## 1.4目标读者
在MNO，SIM解决方案提供商，机器对机器设备供应商，标准组织，网络基础设施供应商，服务提供商和其他行业机构内部工作的技术专家。 

## 1.5术语定义
## 1.6缩略语
## 1.7参考资料

# 2基本原理和假设
本节包含与嵌入式UICC GSMA远程供应系统相关的原理和假设。 
## 2.1基本原则
BPR1解决方案应反映UICC相关最重要的使用案例，并在eUICC硬件不容易从机器到机器设备进行访问或移除的情况下给予充分支持。由于eUICC的性质不同，可能会涵盖并非所有的当前使用案例。 
BPR2解决方案的设计应能够提供新的商业机会，例如在M2M领域，同时保持现有UICC的成熟效益。 
BPR3 eUICC及其整体管理流程的安全必须在任何时候，在任何情况下都至少与目前可拆卸的UICC及其供应流程一样好。 
BPR4 eUICC上可以使用当前UICC上的任何功能，特性或服务。 
BPR5 eUICC对功能，特性或业务的访问应与当前UICC相同，即对终端和用户透明。 
BPR6对eUICC的功能，特性或服务的远程管理应对运营商现有的系统和基础设施产生最小的影响。这应通过尽可能使用现有的标准和规范来实现。 
BPR7保持简单。复杂性被理解为一个风险因素。合理有限的功能方法将支持产品上市时间的预期，并可能随着未来的需求和改进而发展。 
BPR8第三方应用程序在操作profile之外不属于本文档的范围。 
BPR9各实体应对其运营负责。 
BPR10禁用的操作profile中的应用程序和文件系统既不是本地的，也不是远程可选的。 

## 2.2一般假设
### 2.2.1现有标准的使用
STD1 eUICC和相关供应系统的定义应当尽可能有效地体现所有相关方的努力和成本。这应通过尽可能使用现有的标准和规范来实现。 
STD2全球平台规范将被视为实施eUICC的首选框架。 

### 2.2.2机器对机器设备的影响
DEV1 eUICC生态系统的实施对设备的影响很小。 
DEV2没有安全认证要求将被放置在设备上。 
DEV3没有新的认证要求放置在设备上。 
DEV4任何设备批准的影响应包括在现有的设备型号认证或认证方案下，并且独立于eUICC的认证。 
DEV5设备内的通信模块应符合ETSI TS 102 221 [102221]内针对所有标准ETSI格式要素的终端要求。 
DEV6设备制造商应确保设备或服务提供商的所有者有权访问eUICC标识（EID）。
DEV7设备制造商应该在设备上打印eUICC标识（EID），以便人类可读。 

### 2.2.3安全性
SEC1 eUICC的总体安全性与相关管理过程必须始终在任何情况下至少与当前的可拆卸UICC及其供应过程相当。 
SEC2 eUICC及其远程供应系统的体系结构符合3GPP TS 21.133 [21133]“3G安全，安全威胁和要求”的要求。 
SEC3体系结构必须支持与保护操作员证书有关的安全级别，该级别至少等于当前的安全级别。这尤其适用于：
•密钥和认证参数的机密性。 
•订户身份的完整性（例如IMSI）。
SEC4 对于eUICC，认证将成为强制性的，因为包含运营商证书的实体可能不再处于MNO的设计控制之下。 
SEC5远程供应架构必须避免损害客户数据的安全性。 
SEC6可信系统是一个依赖于特定范围的系统来执行指定的安全策略。信任模型被定义为安全相关项目可交付成果的一部分。 
SEC7对于平台和profile管理，涉及管理的所有实体都必须经过相互认证。 

### 2.2.4 Regulatory
REG1监管问题被认为不在嵌入式SIM快速通道工作组的范围之内。监管问题将提交给GSMA监管团队。 

## 2.3 eUICC生态系统
ECO1订阅管理功能由两个角色 - SM-DP和SM-SR提供。 
ECO2 
1.profile管理由MNOprofile和SM-SR中包含的策略规则管理。 
2.政策规则由eUICC和SM-SR代表MNO执行。 
3.政策规则的控制在于MNO。 

### 2.3.1角色和实体
#### 2.3.1.1 eUICC制造商
MAN1 
1.eUICC的制造商提供包含profile和/或一个或多个操作profile的eUICC。 
2.eUICC交付给机器以加工设备制造商。 
3.相关平台管理证书被转发到SM-SR以与每个eUICC相关联。 
4.eUICC制造商负责eUICC的初始密码配置和安全架构。 
5.eUICC制造商的产品和流程的相关部分通过了GSMA认可的认证流程。 
MAN2 EUM应颁发eUICC证书以允许：
•eUICC对其他实体的认证和认证; 
•在SM-DP和eUICC之间建立认证的密钥集; 
•在SM-SR和eUICC之间建立认证的密钥集。 
MAN3 EUM证书和根证书应使用可靠的存储和通信渠道交付给其他实体。 
MAN4 EUM应向SM-DP提供服务，工具，脚本或文档，以便为EUM生成的eUICC创建非个性化的profile。代表SM-DP创建非个性化profile不是EUM的作用。 

#### 2.3.1.2机器到机器设备制造商
DMA1 
1.设备制造商构建机器到机器包含通信模块和eUICC的设备。 
2.预配置profile和/或操作profile可以默认启用。
注意：任何启用的profile都需要各自的MNO的同意。 
#### 2.3.1.3移动网络运营商（MNO）
MNO1 
1.MNO提供移动网络连接。 
2.MNO选择至少一个SM-DP。 
3.MNO应具有到SM-SR的直接接口。 
4.如果客户选择了移动网络运营商，则该移动网络运营商将根据当前的“策略规则”向特定的目标eUICC发起特定profile的下载。 5.移动运营商指定profile特征以及任何特征和应用，类似于当前的UICC。 MNO拥有该profile。
6.profile可以在订购下载时生成。 
7.为了实现与现有UICC流程和接口的透明配合，profile也可以批量订购，然后安全存储在SM-DP中，直到订购下载为止。 SAS要求适用。 
8.当启用此profile时，MNO定义了控制profile管理的策略规则。 
9.命令下载后，MNO应能够在下载profile之前检查并验证目标eUICC的认证和功能（制造商，内存大小，算法等）。 
10.移动网络运营商将收到成功完成的下载和安装profile的确认。 
11.启用的MNO应能够使用OTA平台来管理eUICC中启用的profile的内容。 
MNO2应客户的请求，移动网络运营商应能向相关实体申报将设备加密机器被盗的机器，以便采取适当措施。 
MNO3移动网络运营商应该只使用profile向设备提供有限的服务; MNO用来执行这项有限服务的机制超出了这种架构的范围。 

#### 2.3.1.4 MNO客户
CUS1 
1.MNO客户是MNO订阅的实际合约伙伴。他可能与最终用户不一样。 
2.MNO客户使用一台机器加工来自设备制造商的装备有eUICC的设备和来自所选MNO的profile（订购）。 
3.在下载profile之前，MNO客户必须提供其隐含或明确的接受。 
4.MNO客户直接或间接识别机器以加工设备。机器识别机器设备应隐式或明确识别eUICC。 

#### 2.3.1.5最终用户
END1 
1.最终用户使用本机器加工设备以及与启用profile相关的服务。 2.最终用户可以与MNO客户相同。 
3.eUICC对最终用户是透明的。 4.最终用户与MNO客户或MNO直接相关。 

#### 2.3.1.6订购管理器 - 数据准备（SM-DP）
SMDP1 
1.SM-DP代表MNO行事。 
2.SM-DP从MNO接收profile描述并相应地创建非个人化profile。 SM-DP可能不得不利用EUM提供的工具来创建非个性化profile。 SM-DP和EUM之间交换的信息不是标准化的，可能因不同实体而有所不同。 
3.SM-DP基于来自MNO的输入数据为目标eUICC生成个性化数据（例如，网络访问证书和其他数据）。 
4.SM-DP为目标eUICC构建个性化profile。 
5.SM-DP应使用目标eUICC的Profile Installer证书保护Profile软件包。 
6.SM-DP通过SM-SR在eUICC上安装Personalized Profile。 

SMDP2在MNO的请求下，SM-DP还启动profile启用，以及通过SM-SR向eUICC发送profile删除请求。 
SMDP3 SM-DP为eUICC建立一个安全和认证的通道，以下载和安装profile到eUICC。 
SMDP4 SM-DP和SM-SR之间的接口应具有适当的安全级别，以便支持向SM-SR安全传送profile。 
SMDP5 SM-DP必须始终接收来自MNO的通过SMSR发送profile到eUICC的请求。 
SMDP6 SM-DP至少应通过GSMA SAS认证。 
SMDP7鉴于任何eUICC，SM-DP应能够为此eUICC生成个性化profile。 
SMDP8 SM-DP和MNO是唯一允许为eUICC建立安全和认证通道来管理profile的实体。 
SMDP9 MNO应能够与MNO选择服务任何MNO批准的eUICC的SM-DP接口。 
SMDP10 SM-DP应能够生成可下载并安装在MNO所针对的eUICC上的个性化profile。 
SMDP11 SM-DP应支持本文档第3.5.3节中描述的profile订购程序。 

#### 2.3.1.7订购管理器 - 安全路由（SM-SR）
SMSR1 SM-SR是唯一允许为eUICC建立安全和认证传输信道以管理eUICC平台的实体。 
SMSR2 SM-SR根据MNO的策略规则加载，启用，禁用和删除eUICC上的profile。 
SMSR3 SM-SR从eUICC制造商或以前的SM-SR获取eUICC的平台管理凭证。 
SMSR4在任何时间点，只有一个SM-SR可以与eUICC相关联，但可以在eUICC的生命周期中更改。 
SMSR5 SM-SR和eUICC之间的接口应具有适当的安全级别，以支持eUICC中的profile的安全传送和管理。 
SMSR6 
1.SM-SR不得以明文形式处理操作员证书。 
2.SM-SR具有到SM-DP，eUICC和MNO的安全通信通道。 
3.SM-SR至少应通过GSMA SAS认证。 

SMSR7 SM-SR应能够确定eUICC是否可用于远程管理。 
SMSR8 SM-SR对于生态系统内的其他实体不应具有歧视性。 

#### 2.3.1.8证书颁发者
CIS1证书颁发者角色为嵌入式UICC远程供应系统实体颁发证书，并充当可信第三方，用于对系统实体进行身份验证。 
CIS2证书颁发者为EUM，SM-SR，SM-DP和MNO提供证书。 
CIS3证书颁发者通过超出本规范范围的接口与MNO，SM-SR，SM-DP和EUM进行通信。 

#### 2.3.1.9发起者
INT1发起者是一个可以由各种实体承担的虚拟角色。发起人负责启动特定程序。 
INT2为了本文件中定义的程序的目的，发起者可以假定为MNO。 
INT3在任何时候，只有一个实体可以承担发起者角色。 
INT4发起方和SM-SR之间的接口基于本文档中定义的接口。 
INT5发起者应由SM-SR授权和认证。 

## 2.4 eUICC 
EUICC1 eUICC是标准化ETSI规格中的分立硬件组件。 
EUICC2通常，eUICC是不可移动的。 
EUICC3从机器到机器设备的角度来看，eUICC的行为通常与UICC相同。 
EUICC4 1. eUICC可以包含一个或多个profile。 2.在任何时间点只能启用一个profile。 3. eUICC应包含一个具有回退属性集的profile。只有一个profile可以设置“回退属性”。 4.具有回退属性集的profile不能删除。 5.回退属性的设置由SM-SR管理。 6.适用所有相关的UICC规范。 
EUICC5 eUICC中的（U）SIM或ISIM在profile中的行为预计与当前（U）SIM或ISIM相同。预期不会更改现有的3GPP（U）SIM和ISIM规范。 
EUICC6 eUICC将实施Milenage网络认证算法。 
EUICC7当TUAK包含在3GPP规范中时，eUICC应该在Milenage之外实施TUAK算法。 
EUICC8物理eUICC的所有权可能会在其整个生命周期内发生变化。 
EUICC9 eUICC应包含其相关SM-SR的身份并具有对其进行鉴定的手段。 eUICC制造商交付的
EUICC10 eUICC应始终注册到SMSR。 
EUICC11如果profile启用，profile禁用和profile下载和安装等任何命令未成功完成，则eUICC应在收到请求之前保持其所处的状态。 

### 2.4.1简介
PRO 1简介是发行MNO的财产。 
PRO 2profile应有唯一标识。 
PRO 3 1.在任何时间点只能启用一个profile。 2.嵌入式UICC上可能存在其他profile，但启用/禁用profile始终只是由代表运营商的SM-SR执行的操作。 3.应根据政策规定采取行动。 
PRO 4 1.简介由发行MNO控制。 2.profile与eUICC一起承载UICC的所有逻辑特性。所有相关的UICC规范应适用于eUICC规范定义的例外。 
PRO 5每个profile应在其专用安全容器内隔离。应考虑GlobalPlatform的安全域框架。 
PRO 6profile可用于配置（profile）或操作（操作profile）。他们显然是有区别的。 1.操作profile可以用作配置profile。 2.profile不能用作操作profile。 
PRO 7总是会有一个profile。 
PRO 8可能有几个操作profile。 
PRO 9已安装的profile可以具有以下状态之一：•已启用•已禁用
PRO 10在所有操作使用中，eUICC应强制在任何时间点启用该profile并且只有一个profile已启用。 
PRO 11将有一个由eUICC启动的使用回退属性集启用profile的功能。具有回退属性集的profile将因此提供网络连接，以允许SM-SR远程管理eUICC。 
PRO 12机器不会对机器进行本地profile管理。
PRO 13 Aprofile包含认证算法的参数（例如Milenage算法的OPc，ri，ci），但不包括算法本身。 PRO 14 eUICC可支持其他网络认证算法;如果支持这样的算法，eUICC应实现一种机制来配置其参数。注意：这些其他网络身份验证算法对profile的可访问性超出了本文的范围。 PRO 15 Aprofile包含策略规则的子集以控制外部profile管理操作。 PRO 16 Aprofile可能包含生态系统中实体的标识符，密钥，PIN，证书，算法参数以及第一和第二级应用程序。 （参考：ETSI TS 102 221 [102221]）PRO 17在当前UICC上可能的任何功能，特性或服务应该可以在eUICC上的操作profile中实现。 
PRO 18在eUICC上对profile中功能，特性或服务的访问应与当前的UICC相同，即对终端和用户透明。 
PRO 19在eUICC上对profile中的功能，特性或服务进行远程管理应该对运营商现有的系统和基础设施产生最小的影响。 
PRO 20profile仅存储在SM-DP中并安装在eUICC上;它们不存储在其他地方并且在传输中加密。 

### 2.4.2策略和策略控制
PPC1每个profile都有相关的策略。政策包含管理profile操作状态更改的规则。这些状态转换是：•禁用•启用•删除
PPC2更新 - 对profile策略的访问仅限于发放MNO。 
PPC3禁用profile的策略规则只能应用于自身。禁用的profile的策略规则不能影响任何其他profile。
