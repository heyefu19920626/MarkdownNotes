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
> /usr/lib/systemd/system #service文件目录

- .service文件内容
```
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=vstb engine service

[Service]
User=root
Type=simple
ExecStart=/home/cjs/bin/vstb_engine vengine_cfg.json 
WorkingDirectory=/home/cjs/bin
Restart=always

[Install]
WantedBy=multi-user.target
```

#### 日志文件
> /var/log/