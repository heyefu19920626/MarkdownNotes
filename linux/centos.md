# Centos

- [..](linux-catalog.md)


- [Centos](#centos)
  - [Centos7开放及查看端口](#centos7开放及查看端口)
  - [Centos7设置IP](#centos7设置ip)
  - [yum源](#yum源)
    - [挂载本地ISO镜像源](#挂载本地iso镜像源)


## Centos7开放及查看端口

1. 开放端口
   1. `firewall-cmd --zone=public --add-port=5672/tcp --permanent`   # 开放5672端口
   2. `firewall-cmd --zone=public --remove-port=5672/tcp --permanent`  #关闭5672端口
   3. `firewall-cmd --reload`   # 配置立即生效

2. 查看防火墙所有开放的端口
   1. `firewall-cmd --zone=public --list-ports`

3. 关闭防火墙
   1. `systemctl stop firewalld.service`

4. 查看防火墙状态
   1. `firewall-cmd --state`

5. 查看监听的端口
    1. `netstat -lnpt`

## Centos7设置IP

1. 使用`ip a`查询所有的IP及网卡
2. 修改/etc/sysconfig/network-scripts目录下对应的网卡文件(ifcfg-网卡名)
```sh
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
#BOOTPROTO=dhcp
BOOTPROTO=static # 修改为手动分配
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=e518558f-65a8-4963-bac1-484a94ee07c7
DEVICE=ens33
#ONBOOT=no
ONBOOT=yes #改为yes
IPADDR=192.168.145.250 # 本机的IP
NETMASK=255.255.255.0 # 子网掩码
GATEWAY=192.168.145.2 # 网关
DNS1=8.8.8.8 # DNS，否则ping不通网址
```
3. 使用命令`systemctl restart network`重启网络
4. 使用`ip a`查询是否配置成功

## yum源

### 挂载本地ISO镜像源

1. 在虚拟机上挂载对应的ISO镜像
2. 在/mnt目录下创建cdrom目录, `mkdir -p /mnt/cdrom`
3. 使用`mount -t auto /dev/cdrom /mnt/cdrom`挂载, 取消挂载使用`umount /mnt/cdrom`
4. 查看是否挂载成功`ls /mnt/cdrom`
5. 进入yum配置目录`cd /etc/yum.repos.d`
6. 修改配置文件`vi CentOS-Base.repo`
```sh
[base]
name=CentOS-$releasever - Base
baseurl=file:///mnt/cdrom
gpgcheck=0
enable=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-$releasever - Updates
baseurl=file:///mnt/cdrom
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever - Extras
baseurl=file:///mnt/cdrom
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

name=CentOS-$releasever - Plus
baseurl=file:///mnt/cdrom
gpgcheck=0
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```
7. 清楚yum缓存`yum clean all`并缓存yum信息`yum makecache`
8. 查看是否成功`yum list`

## 进入单用户模式

1. 在启动界面选中内核，然后按e
2. 在linux/linux16开始的行尾添加init=/bin/bash或者single，根据提示按Ctrl+X启动
3. `mount | grep ' / '`查询挂载模式，包含ro表示只读
4. 使用`mount -o remount,rw /`重新挂载为可读写
5. 之后就可以去修改配置文件等