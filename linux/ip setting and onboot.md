#### IP设置

> systemctl status network #查看网络服务状态
> /etc/sysconfig/network-scripts #网卡配置在该目录下
> ifcfg-网卡名称
> systemctl start network

##### ifcfg-eth0
```
TYPE=Ethernet #网卡协议类型
DEVICE=eth0 #物理设备名称（这里只是一个逻辑名）
DEFROUTE=yes #就是default route，是否把这个eth设置为默认路由
ONBOOT=yes #系统启动时是否自动加载该网卡 yes|no
BOOTPROTO=static #获取地址协议 static|bootp|dhcp
IPADDR=192.168.10.165 # ip地址
NETMASK=255.255.0.0 # ip对应的子网掩码
GATEWAY=192.168.1.254 #ip对应的网关地址
DNS1=192.168.1.254 #指定DNS1地址
DNS2=10.165.1.6 #指定DNS2地址（有的话配，没有就不配置）
NM_CONTROLLED=no #是否由Network Manager 控制该网络接口。修改保存后立即生效，无需重启。但有坑，建议设置为no
USERCTL=yes #非ROOT用户是否允许控制整个设备 yes|no
IPV6INIT=yes #是否执行IPv6 yes:支持IPv6 no:不支持IPv6
```

#### 开机自启
> /usr/lib/systemd/system #service文件目录(centos)
> /etc/systemd/system #service文件目录(ubuntu)
> systemctl enable  httpd
> systemctl is-enabled  httpd
> systemctl disable  httpd
> systemctl list-unit-files|grep enabled      #查看自启动服务列表

[Service文件详解](https://blog.csdn.net/Mr_Yang__/article/details/84133783)

- .service文件内容
```
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit] #启动顺序与依赖关系
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.service # After：在network.target,auditd.service启动后才启动
# Before
Wants=sshd-keygen.service # Wants字段：表示sshd.service与sshd-keygen.service之间存在"弱依赖"关系
# Requires字段则表示"强依赖"关系，即如果该服务启动失败或异常退出，那么sshd.service也必须退出

[Service] #启动行为
EnvironmentFile=/etc/sysconfig/sshd #指定当前服务的环境参数文件
ExecStart=/usr/sbin/sshd -D $OPTIONS #定义启动进程时执行的命令
ExecReload=/bin/kill -HUP $MAINPID #重启服务时执行的命令
# 所有的启动设置之前，都可以加上一个连词号（-），表示"抑制错误"
Type=simple #启动类型
KillMode=process
Restart=on-failure #重启方式
RestartSec=42s

[Install] #定义如何安装这个配置文件，即怎样做到开机启动
WantedBy=multi-user.targe
```

#### 日志文件
> /var/log/