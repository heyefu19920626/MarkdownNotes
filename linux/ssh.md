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

## 开启空闲自动断连

1. 在配置文件中增加
```bash
# 表示每分钟发送一次, 然后客户端响应, 这样就保持长连接了.
ClientAliveInterval 60
# ClientAliveCountMax表示服务器发出请求后客户端没有响应的次数达到一定值, 就自动断开.
ClientAliveCountMax 3 
```
2. 重启ssh服务
3. 如果不生效，`sudo vim /etc/profile`, 追加TMOUT环境变量
```bash
export TMOUT=600
```