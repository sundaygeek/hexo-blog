---
title: openBTS part6
date: 2018-05-16 10:14:58
tags:
- OpenBTS
---

# 第6章OpenRegistration
OpenRegistration是OpenBTS特有的功能，可为移动网络提供WiFi专用门户实施。任何使用机场或酒店的公共WiFi连接的人都熟悉门禁门户。您的设备可以连接到WiFi网络，但在您回答问题，观看广告，输入PIN等设备之前，将被拒绝访问某些功能。设备用于自行设置。同样，OpenRegistration允许手机以最初限制访问的方式加入移动网络。它可以拨出，但手机没有分配的号码，因此不能由网络中的其他参与者呼叫。但是，它可以用于通过SMS提供自己的号码。这种类型的网络在用户临时性和流动性或网络本身只是临时需要的任何临时安装中非常有用：应急响应，远程工作区域，旅游目的地，大型节日等等。因为管理员不是需要创建账户和分配号码，OpenRegistration网络更容易部署，对用户来说仍然非常有用。

## 启用
要开始使用OpenRegistration网络，必须启用功能本身。首先，看一下键：
```
OpenBTS> config OpenRegistration
Control.LUR.OpenRegistration (disabled) [default]
Control.LUR.OpenRegistration.Message Welcome to the test network. Your
IMSI is [default]
Control.LUR.OpenRegistration.Reject (disabled) [default]
Control.LUR.OpenRegistration.ShortCode 101 [default]
```
要启用OpenRegistration，必须将Control.LUR.OpenRegistration键设置为正则表达式。正则表达式（有时写成“正则表达式”）是定义要匹配的模式的一种方式。他们有一些标准，更多的信息可以在Wikipedia上找到。匹配此正则表达式的IMSI将被授予访问网络的权限。表6-1列举了一些模式的例子及其效果。
Table 6-1. OpenRegistration regular expressions

Regular Expression| OpenRegistration Effect
---------- | ------------
.\*| Match all IMSIs
^460| Match any IMSI starting with “460”, the MCC for China
^46002| Match any IMSI from China Mobile (MCC=460, MNC=02)
460027217080245| Match only IMSI “460027217080245”
0| Match any IMSI containing a “0”
1| Match any IMSI containing a “1”
1234$| Match any IMSI ending in “1234”

对于本书，OpenRegistration将被启用以接受它遇到的任何IMSI：
```
OpenBTS> config Control.LUR.OpenRegistration .*
Control.LUR.OpenRegistration changed from "" to ".*"
```
还有一个额外的键允许明确拒绝它匹配的IMSI。如果您希望允许除特定组以外的每个IMSI，这很方便。再次从上面的表格中，设置网络明确拒绝来自中国移动的任何IMSI：
```
OpenBTS> config Control.LUR.OpenRegistration.Reject ^46002
Control.LUR.OpenRegistration.Reject changed from "" to "^46002"
```
确保您有信心在野外部署它们之前了解这些模式如何工作。噩梦般的情况是有人无意中加入了你的开放网络，心脏病发作，并且因为你的网络不支持它而无法呼叫紧急服务。仔细检查您的网络的GSM.RACH.AC仍然被设置为默认值0x0400。这会在您的信标中宣传“不支持紧急呼叫”。

## 个性化
加入你的网络的用户将会看到Control.LUR.Open Registration.Reject键的内容。现在更改为与您的安装更相关：
```
OpenBTS> config Control.LUR.OpenRegistration.Message Welcome to IslandNet! Call
or text 101 for assistance. Your IMSI is:
Control.LUR.OpenRegistration.Message changed from "Welcome to the test network.
Your IMSI is " to "Welcome to IslandNet! Call or text 101 for assistance. Your
IMSI is: "
```
这指示新加入的成员通过拨打101寻求帮助。您的网络应该有人分配给101号码或在那里实施自动语音菜单。用户也可以按照指示向该号码发送短信。默认情况下，SMQueue具有101号码的简码处理程序，并将开始与用户进行对话，以获得指定的电话号码。 SMQueue中的SC.Register。参数控制使用哪种短代码以及消息的措辞，以及允许用户可选的数字范围。 SMQueue尚未具有CLI界面，但nmcli.py可用于读取和修改这些参数：
```
$ ./nmcli.py smqueue config read
$ ./nmcli.py smqueue config update Configuration.Key.Name new-value
```
如果您想使用101以外的数字，则应确保将OpenBTS Control.LUR.OpenRegistration.ShortCode设置为你的新号码。该键设置欢迎短信的源地址。如果它被设置为您的新帮助号码，用户可以简单地回复初始消息。

## 禁用
要再次禁用OpenRegistration，必须重置接受和拒绝模式：
```
OpenBTS> unconfig Control.LUR.OpenRegistration
Control.LUR.OpenRegistration disabled
OpenBTS> unconfig Control.LUR.OpenRegistration.Reject
Control.LUR.OpenRegistration.Reject disabled
```
OpenBTS现在已恢复为使用订户数据库来授予或拒绝访问，而不是IMSI模式。

