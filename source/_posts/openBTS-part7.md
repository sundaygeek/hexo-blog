---
title: openBTS part7
date: 2018-05-16 10:15:11
tags:
- OpenBTS
---

# 第7章NodeManager API 
NodeManager是用于系统配置和监视的跨OpenBTS，SMQueue和SIPAuthServe的通用控制接口。所有组件都支持一些通用功能（如配置操作），并使用可用于组件特定任务或数据源的专用API。 NodeManager API分为两大类：请求/响应和流。这两种类型都使用JSON格式的消息并通过ZeroMQ进行通信，ZeroMQ是一个在进程间创建简单且可靠的套接字连接的库。实施NodeManager API以简化多个OpenBTS实例的远程管理，以及SMQueue和SIPAuthServe等中央服务。 API是互联网的正常组成部分，因此这可能看起来不太令人兴奋。但是，当阅读第74页上的“PhysicalStatus API”时，请牢记图I-1。不需要十几种协议和实体 - 核心无线电测量数据现在可直接作为JSON消息流使用！其他API将在未来的发行版中添加到NodeManager界面中。 

## nmcli.py
在名为nmcli.py的NodeManager存储库中包含一个小型Python实用程序。它需要安装一些依赖项，但build.sh脚本（在第9页的“构建代码”中）应该已经完成​​了。为了确保拥有它们，下面是手动命令：
```
$ sudo add-apt-repository -y ppa:chris-lea/zeromq
$ sudo apt-get update
$ sudo apt-get install libzmq3-dev libzmq3 python-zmq
```
nmcli.py的功能总是在新的NodeManager API实现时扩展。要查看当前使用情况，请运行nmcli.py，不要使用任何参数：
```
$ ./nmcli.py
```
nmcli.py的实用程序不是作为NodeManager API的专业接口，而是用于快速格式化消息并在实现时测试新API端点的开发工具。它将在本章中用于演示不同的API端点。首先列出nmcli.py的用法，然后是交换格式化的JSON消息。

## 版本API
所有组件都实现版本，请求/响应API。这是一个简单的API，但公开了一个非常重要的关于在远程节点上运行的组件的信息：它的当前版本。现在使用nmcli.py向SMQueue询问它的版本：
```
$ ./nmcli.py smqueue version
```
它发送给SMQueue的JSON请求非常简单：
```
{
    "command" : "version"
}
```
...并且回应同样是明确的：
```
{
    "code" : 200,
    "data" : "release 5.0.0-prealpha+667f928701 CommonLibs:3ad343b97b built 2014-09-17T21:26:56 "
}
```

## 配置API
所有组件都实现config，一个请求/响应API。该API用于修改每个组件中的配置值。每个组件的配置模式在接受请求之前对其进行评估，这使得API更适合于简单地更新适当的SQLite3数据库中的值。

### 读取所有键
现在使用nmcli.py向SMQueue询问所有配置参数：
```
$ ./nmcli.py smqueue config read
```
它使用“config”命令和“read”操作生成请求：
```
{
    "command" : "config",
    "action" : "read"
}
```
SMQueue以其支持的每个配置键的巨大列表响应（剪切为空间）：
```
{
    "code" : 200,
    "data" : [
    ...
    ]
}
```

### 读取一个键
API还支持读取特定键。现在使用nmcli.py向SC.Register.Code参数询问SMQueue：
```
$ ./nmcli.py smqueue config read SC.Register.Code
```
它使用“config”命令和“read”的操作以及SC.Register.Code的新关键字段生成请求：
```
{
    "command" : "config",
    "action" : "read",
    "key" : "SC.Register.Code"
}
```
SMQueue响应使用此特定配置键的模式和值信息：+
```
{
    "code" : 200,
    "data" : {
        "defaultValue" : "101",
        "description" : "Short code to the application which registers the sender to the system.",
        "key" : "SC.Register.Code",
        "scope" : 0,
        "static" : false,
        "type" : "string",
        "units" : "",
        "validValues" : "^[0-9]{3,6}$",
        "value" : "101",
        "visibility" : "customer - can be freely changed by the customer without any detriment to their system"
    }
}
```
请求不存在的键将导致类似于组件的404响应：

```
{
    "code" : 404
}
```

### 更新
如果配置API不允许设置新值，那么配置API不会太有用。消息的操作字段将更改为“更新”，并且必须指定一个包含所需更改的新值字段。使用nmcli.py来更新我们查询的最后一个键--SC.Register.Code-为555而不是101：
```
$ ./nmcli.py smqueue config update SC.Register.Code 555
```
注意新的值字段和更新的动作字段：
```
{
    "command" : "config",
    "action" : "update",
    "key" : "SC.Register.Code",
    "value" : "555"
}
```
响应包括两条信息：
```
{
    "code" : 204,
    "dirty" : 0
}
```
204代码表示动作是成功但没有需要报告的数据。肮脏的领域让你知道，没有任何需要应用的静态键，也就是说，你的配置是活的。如果脏不为零，则最终需要重新启动组件才能完全应用更改。以下是其他响应方式：
•304：无需更改，旧值和新值匹配
•404：未知键（与读命令类似）
•406：新值无效
•409：新值与另一配置键冲突
•500 ：数据库错误，存储新值失败

## PhysicalStatus API 
OpenBTS是实现PhysicalStatus（流API）的唯一组件。它没有被默认激活;使用NodeManager.API.PhysicalStatus键来启用它。为了保持向后兼容性，PhysicalStatus API在发布时支持不同的模式。目前，只有模式0.1已经实施。将键设置为值0.1将使用模式0.1激活PhysicalStatus API和流事件数据。由于您已经有了nmcli.py，所以现在就使用它来激活API：
```
$ ./nmcli.py openbts config update NodeManager.API.PhysicalStatus 0.1
```
在激活此流后，您需要附加一个客户端来查看正在生成的数据。 OpenBTS / apps存储库目录中提供了一个名为JSONEventsClient.cpp的示例客户端。它在构建OpenBTS时默认编译，但在使用该包时未安装到系统上。该客户端连接到由NodeManager.Events.Port定义的ZeroMQ端口，并等待API发布事件。立即进入OpenBTS源代码的克隆版并执行客户端：
```
$ cd dev/openbts/apps
$ ./JSONEventsClient
```
在OpenBTS安装中存在活动之前，没有什么有趣的事情会发生。重启手机，发送短信或拨打电话，PhysicalStatus读数将开始显示在控制台上。这些读数的例子如下：
```
{
    "name" : "PhysicalStatus",
    "timestamp" : "18446744072283447705",
    "version" : "0.1",
    "data" : {
        "burst" : {
            "RSSI" : -49.4808,
            "RSSP" : -27.4808,
            "actualMSPower" : 11,
            "actualMSTimingAdvance" : 0,
            "timingError" : 1.59709
        },
        "channel" : {
            "ARFCN" : 153,
            "IMSI" : "001010000000001",
            "carrierNumber" : 0,
            "timeslotNumber" : 0,
            "typeAndOffset" : "SDCCH/4-1",
            "uplinkFrameErrorRate" : 0
        },
        "reports" : {
            "neighboringCells" : [],
            "servingCell" : {
                "RXLEVEL_FULL_dBm" : -67,
                "RXLEVEL_SUB_dBm" : -67,
                "RXQUALITY_FULL_BER" : 0,
                "RXQUALITY_SUB_BER" : 0
            }
        }
    }
}
```
这些读数显示手机和基站之间的业务流量时的物理无线电突发信息。这些读数也称为测量报告，作为GSM标准的一部分发生在背景中。它们为基站提供用于适当调整手机发射功率的信息，触发切换消息并计算定时提前。那么访问这些数据的价值是什么？许多应用程序可以使用这些有关网络无线电条件的原始元信息来做出明智的功耗决策，更加智能地分担负载，等等。它目前正在用于搜索和救援行动。冰岛的直升机配备了运行OpenBTS的便携式基站，并且正在飞往偏远地区，在那里已知失去远足者。 OpenRegistration配置为使该区域中的所有手机都自动允许加入网络。当网络发送测试SMS消息时，来自基站和手机的无线电接收电平和发射功率被不断记录。除了这些无线电信息外，还记录了直升机当前的纬度，经度和高度。然后可以对这些信息进行算法分析，以确定在高达35公里的距离内65米范围内任何手机在该区域的位置。关于这个项目的更多信息可以在这里找到。

