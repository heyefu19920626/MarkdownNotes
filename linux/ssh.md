# Ssh


## 启动

> service ssh start

## 开启远程访问

1. `sudo vim /etc/ssh/sshd_config`
```bash
# 允许root登录
PermitRootLogin yes 
Port = 22 # 去掉前面的#号
ListenAddress 0.0.0.0		#去掉前面的#号
PasswordAuthentication yes # 将 no 改为 yes 表示使用帐号密码方式登录
```
2. `sudo service ssh restart	#重启SSH服务`