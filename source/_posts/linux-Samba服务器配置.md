---
title: linux-Samba服务器配置
date: 2018-04-11 14:14:27
tags:
  - linux
categories:
  - linux
---


# 1.安装samba

```
samba: 
sudo apt-get install samba

smbclient: 
sudo apt-get install smbclient
```


# 2.修改配置文件

sudo vi /etc/samba/smb.conf
在文件末尾加上如下[share]  （相当于在home目录项增加了一个新用户）
```
[share]
comment = Share Folder require password
browseable = yes
path = /home/share
create mask = 0777
directory mask = 0777
valid users = share
force user = nobody
force group = nogroup
public = yes
writable = yes
available = yes 
```

# 3.保存退出后重启samba
sudo /etc/init.d/samba restart

# 4.切换到 /home/目录下创建一个用户目录：
sudo mkdir share

# 5.设置目录的权限：  （777代表最高权限，可读可写可执行）
chmod 777 share

# 6.增加相应的系统账号
sudo groupadd share -g 6000
sudo useradd share -u 6000 -s /shin/nologin -d /dev/null

# 7.最后打开我的电脑空白位置右键添加一个网络位置
输入 \\IP地址（linux）\share   按提示步骤输入用户名和密码。



