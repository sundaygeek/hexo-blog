---
title: openBTS part9 附录A
date: 2018-05-16 10:15:42
tags:
- OpenBTS
---

## 附录A快速参考
以下是遍布本书的许多事实的快速参考。我发现自己经常看这些东西，并想把它们放在一个统一的部分。 

## GSM层次结构
本书不能尝试在短短几章中教给你GSM。然而，掌握一个非常有用的东西是从宏观到微观的GSM术语的层次结构。塔是我们都熟悉的天线，有时伪装成仙人掌或树。每座塔可以分为多个部门。这些部门覆盖了可能的360º的一部分（例如塔上的四个90º扇区）。一个单一的塔楼覆盖360º。每个扇区将被分配一个或多个ARFCN，有时也称为载波，因为它们是实际交换数据的实际频率对。每个ARFCN有八个时隙。每个时隙都有一个分配给它的组合。每个组合都由多个逻辑通道组成。这些逻辑信道被划分为信令和媒体两大类。 SDCCH（独立专用控制信道）携带手机注册流量或SMS流量等信令。 TCH（业务信道）承载诸如GPRS数据或语音业务的媒体。逻辑信道由多个帧组成，帧由突发组成。要查看此系统的实际运行情况，请打开GSM时隙和通道可视化器。

## 分贝和分贝毫瓦分贝
可用于表示两个值之间的比率，或者与基本单位配对时表示绝对值。当以dB表示时，仅表示比率。例如，10dB的SNR意味着信号比噪声强10倍。当写入81dBm时，以毫瓦表示绝对值。例如，GSM手机可以传输最大33 dBm或2 W.需要牢记的一个便利因素是，每次发生3 dB的变化时，该比率会增加一倍或两倍。

Table A-1. dB: Decibels and ratios

 dB | Ratio 
----|----
30 | 1000
20 | 100
10 | 10
6 | ~4
3 | ~2
1 | 1.259
0 | 1
-1 | 0.794
-3 | ~0.5
-6 | ~0.25
-10 | 0.1
-20 | 0.01
-30 | 0.001

Table A-2. dBm: Decibel milliwatts

dBm | Absolute value
---- | ----
30 | 1000 mW or 1 W
20 | 100 mW
10 | 10 mW
6 | 4 mW
3 | 2 mW
1 | 1.3 mW
0 | 1.0 mW or 1000 µW
-1 | 794 µW
-3 | 501 µW
-6 | 251 µW
-10 | 100 µW
-20 | 10 µW
-30 | 1 µW or 1000 nW

Network Ports
Table A-3. Network ports

Port | Type | Setting | Description
---- | ---- | ---- | ----
5062 | UDP | SIP.Local.Port | OpenBTS SIP signaling
5063 | UDP | SIP.Local.Port | SMQueue SIP signaling
5064 | UDP | SIP.Local.Port | SIPAuthServe VoIP SIP signaling
5700 | UDP | TRX.Port | Raw radio samples between OpenBTS and transceiver application
16001 | UDP | Peering.Port | OpenBTS multinode info and event string exchange
16484 | UDP | RTP.Start and RTP.Range | OpenBTS VoIP RTP media
45060 | ZeroMQ RESP | NodeManager.Commands.Port | OpenBTS NodeManager command port
45063 | ZeroMQ RESP | NodeManager.Commands.Port | SMQueue NodeManager command port
45064 | ZeroMQ RESP | NodeManager.Commands.Port | SIPAuthServe NodeManager command port
45160 | ZeroMQ PUB | NodeManager.Events.Port | OpenBTS NodeManager events port
49300 | TCP | CLI.Port | OpenBTS command-line interface string exchange

File Paths
Table A-4. Important files

File path | Description
---- | ----
/etc/OpenBTS/OpenBTS.db | OpenBTS configuration database
/etc/OpenBTS/SIPAuthServe.db | SIPAuthServe configuration database
/etc/OpenBTS/SMQueue.db | SMQueue configuration database
/var/log/OpenBTS.log | Logging destination for events from all components
/etc/asterisk/sip.conf | Extra SIP account configuration for Asterisk
/etc/asterisk/extensions.conf | Extra dialplan configuration for Asterisk
/var/lib/asterisk/sqlite3dir/sqlite3.db | Subscriber registry database used by SIPAuthServe, SMQueue, and Asterisk

