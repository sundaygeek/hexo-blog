---
title: linux防火墙设置
date: 2018-04-11 23:40:12
tags:
  - linux
---

# UFW防火墙
UFW防火墙是一个主机端的iptables类防火墙配置工具。这个工具的目的是提供给用户一个可以轻松驾驭的界面，就像包集成和动态检测开放的端口一样。

自2.4版本以后的Linux内核中， 提供了一个非常优秀的防火墙工具。这个工具可以对出入服务的网络数据进行分割、过滤、转发等等细微的控制，进而实现诸如防火墙、NAT等功能。
一般来说， 我们会使用名气比较的大iptables等程序对这个防火墙的规则进行管理。iptables可以灵活的定义防火墙规则，功能非常强大。但是由此产生的副作用便是配置过于复杂。一向以简单易用著称Ubuntu在它的发行版中，附带了一个相对iptables简单很多的防火墙配置工具：ufw。
ufw默认是没有启用的。也就是说， ubuntu中的端口默认都是开放的。使用如下命令启动ufw：

# UFW安装与使用
1. 安装
sudo apt-get install ufw

2. 启用
sudo ufw enable
sudo ufw default deny
运行以上两条命令后，开启了防火墙，并在系统启动时自动开启。关闭所有外部对本机的访问，但本机访问外部正常。

3. 开启/禁用
sudo ufw allow|deny [service]
打开或关闭某个端口，例如：
sudo ufw allow smtp　允许所有的外部IP访问本机的25/tcp (smtp)端口
sudo ufw allow 22/tcp 允许所有的外部IP访问本机的22/tcp (ssh)端口
sudo ufw allow 53 允许外部访问53端口(tcp/udp)
sudo ufw allow from 192.168.1.100 允许此IP访问所有的本机端口
sudo ufw allow proto udp 192.168.0.1 port 53 to 192.168.0.2 port 53
sudo ufw deny smtp 禁止外部访问smtp服务
sudo ufw delete allow smtp 删除上面建立的某条规则

4. 查看防火墙状态
sudo ufw status
一般用户，只需如下设置：
sudo apt-get install ufw
sudo ufw enable
sudo ufw default deny
以上三条命令已经足够安全了，如果你需要开放某些服务，再使用sudo ufw allow开启。
开启/关闭防火墙 (默认设置是’disable’)
sudo ufw enable|disable
转换日志状态
sudo ufw logging on|off
设置默认策略 (比如 “mostly open” vs “mostly closed”)
sudo ufw default allow|deny
许可或者屏蔽端口 (可以在“status” 中查看到服务列表)。可以用“协议：端口”的方式指定一个存在于/etc/services中的服务名称，也可以通过包的meta-data。 ‘allow’ 参数将把条目加入 /etc/ufw/maps ，而 ‘deny’ 则相反。基本语法如下：
sudo ufw allow|deny [service]
显示防火墙和端口的侦听状态，参见 /var/lib/ufw/maps。括号中的数字将不会被显示出来。
sudo ufw status
UFW 使用范例：
允许 53 端口
$ sudo ufw allow 53
禁用 53 端口
$ sudo ufw delete allow 53
允许 80 端口
$ sudo ufw allow 80/tcp
禁用 80 端口
$ sudo ufw delete allow 80/tcp
允许 smtp 端口
$ sudo ufw allow smtp
删除 smtp 端口的许可
$ sudo ufw delete allow smtp
允许某特定 IP
$ sudo ufw allow from 192.168.254.254
删除上面的规则
$ sudo ufw delete allow from 192.168.254.254

5. 加载
sudo ufw reload


# ubuntu iptables设置
删除原来 iptables 里面已经有的规则
iptables -F
iptables -X

抛弃所有不符合三种链规则的数据包
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP

设置：本地进程 lo 的 INPUT 和 OUTPUT 链接 ； eth1的 INPUT链
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i eth1 -m state --state NEW,INVALID -j LOG
iptables -A OUTPUT -o lo -j ACCEPT

对其他主要允许的端口的 OUTPUT设置：
- DNS
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 53 -j ACCEPT
iptables -A OUTPUT -o eth1 -p UDP --sport 1024:65535 --dport 53 -j ACCEPT

- HTTP
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 80 -j ACCEPT

- HTTPS
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 443 -j ACCEPT

- Email 接受 和发送
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 110 -j ACCEPT
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 25 -j ACCEPT

- FTP 数据和控制
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 20 -j ACCEPT
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 21 -j ACCEPT

- DHCP
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 68 -j ACCEPT
iptables -A OUTPUT -o eth1 -p UDP --sport 1024:65535 --dport 68 -j ACCEPT

- POP3S Email安全接收
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 995 -j ACCEPT

- 时间同步服务器 NTP
iptables -A OUTPUT -o eth1 -p TCP --sport 1024:65535 --dport 123 -j ACCEPT

- 拒绝 eth1 其他剩下的
iptables -A OUTPUT -o eth1 --match state --state NEW,INVALID -j LOG


最后是有关于iptables存储的命令：
代码:
iptables-save > /etc/iptables.up.rule -  存在你想存的地方

代码:
iptables-restore < /etc/iptables.up.rules - 调用


因为iptables 在每次机器重新启动以后，需要再次输入或者调用，为了方便操作，使用
代码:
sudo gedit /etc/network/interfaces

在
代码:
auto ath0
iface ath0 inet dhcp
后面加上
代码:
pre-up iptables-restore < /etc/iptables.up.rules - 启动自动调用已存储的iptables

代码:
post-down iptables-save > /etc/iptables.up.rule #关机时，把当前iptables 储存
