# Ssh


## 启动

> service ssh start

## 开启远程访问

1. `sudo vim /etc/ssh/sshd_config`
```bash
# 允许root登录
PermitRootLogin yes 
# 密码身份验证
PasswordAuthentication yes
```