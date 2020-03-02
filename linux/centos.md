# Centos

- [..](linux-catalog.md)

- [Centos](#centos)
  - [Centos7开放及查看端口](#centos7开放及查看端口)


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
