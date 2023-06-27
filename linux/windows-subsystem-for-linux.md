# Windows10上使用Linux子系统(WSL)

- [..](linux-catalog.md)

- [Windows10上使用Linux子系统(WSL)](#windows10上使用linux子系统wsl)
	- [安装](#安装)
	- [升级至WSL2](#升级至wsl2)
	- [在windows访问wsl文件](#在windows访问wsl文件)
	- [忘记密码](#忘记密码)
		- [报错处理](#报错处理)
	- [更换阿里源](#更换阿里源)
	- [安装go](#安装go)
	- [使用docker](#使用docker)
	- [局域网访问WSL](#局域网访问wsl)
	- [CCProxy代理](#ccproxy代理)
	- [配置代理](#配置代理)
	- [闪退报错](#闪退报错)
	- [限制wsl使用的内存](#限制wsl使用的内存)
- [Win10安装Windows Terminal](#win10安装windows-terminal)
	- [Windows Terminal美化](#windows-terminal美化)
	- [pip安装源修改](#pip安装源修改)

## 安装

- 开启开发者模式
    + 设置->更新和安全->开发者选项->开发人员模式
- 在Microsoft store安装Uubuntu 18.04LTS
- 在启用或关闭windows功能勾选适用于Linux的Windows子系统

## 升级至WSL2

- 加入预览版体验计划,更新到最新系统
- 管理员运行在powershell中执行
```
#启用虚拟机平台可选组件
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
#查看当前版本
wsl -l -v
#升级
wsl --set-version Uubuntu-20.04 2
```

## 在windows访问wsl文件

`\\wsl$`

## 忘记密码

> wsl.exe --user root

### 报错处理

1. 确认启用Hyper-V
2. 确认启用Linux子系统
3. 在官网下载wsl2内核安装

## 更换阿里源

- 备份/etc/apt/source.list,编辑并替换为阿里源

```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
```

## 安装go

- 略
- root用户每次都要重新source /etc/profile
    + 在~/.bashrc或~/.profile中添加环境变量

## 使用docker
> export DOCKER_HOST=tcp://localhost:2375

对于运行在WSL中的Docker而言，它会采用类似/mnt/c/Users/Payne/这样的更符合Linux习惯的路径，而Docker for Windows则会使用类似/c/Users/Payne/这样更符合Windows习惯的路径。因此，如果你在使用Docker的过程中，需要处理分区挂载相关的东西，一个比较好的建议是修改WSL的配置文件(如果不存在需要自行创建)
```
sudo nano /etc/wsl.conf
[automount]
root = /
options = "metadata"
```

##  局域网访问WSL

1. 将主机的端口映射到wsl端口
```bash
netsh interface portproxy add v4tov4 listenport=【宿主机windows平台监听端口】 listenaddress=0.0.0.0 connectport=【wsl2平台监听端口】 connectaddress=【wsl2平台ip】protocol=tcp
netsh interface portproxy add v4tov4 listenport=22 listenaddress=0.0.0.0 connectport=22 connectaddress=172.23.204.187 protocol=tcp
或
netsh interface portproxy add v4tov4 listenport=22 listenaddress=* connectport=22 connectaddress=172.23.204.187 protocol=tcp
```
2. 只在主机上可以使用127.0.0.1:port访问wsl2
3. 查看wsl的ip
   1. `cat /etc/resolv.conf`

## CCProxy代理

1. 配置带密码的代理
   1. 选择账号->允许范围（允许部分）， 验证类型(用户名/密码)->编辑->设置用户名/密码->保存->确定
   2. 域用户验证不勾选

## 配置代理

1. windows安装cntlm
   1. 安装在默认位置`C:\Program Files (x86)\Cntlm`
   2. 也可以使用python的Px工具`pip install px-proxy`
2. 编辑cntlm.ini,管理员权限
```ini
Username 用户名
Domain   域名  china.baidu.com
#Password password #注释掉
Proxy    proxyhk.baidu.com:8080
#Proxy       10.0.0.42:8080
#Listen改成0.0.0.0加端口号，这样本机所有IP都可以访问（不改的话只有127.0.0.1可以）
Listen       0.0.0.0:4399
#这三行内容通过下面的步骤3获取
#Auth NTLM 
#PassNT ***
#PassLM ***
```
3. 在安装目录打开命令行执行命令`.\cntlm.exe -c .\cntlm.ini -I -M https://www.google.com`或者`cntlm.exe -H`(-H生成hash，要打开Auth)
4. 将上一步生成的认证信息报备到cntlm.ini中
5. 管理员命令行启动`net start cntlm`
   1. 停止`net stop cntlm`
6. 在wsl中添加需要的根证书文件`sudo vim /usr/local/share/ca-certificates/baidu.crt`,并写入证书内容
```
-----BEGIN CERTIFICATE-----
MIID2TCCAsGgAwIBAgIJALQPO9XxFFZmMA0GCSqGSIb3DQEBCwUAMIGCMQswCQYD
VQQGEwJjbjESMBAGA1UECAwJR3VhbmdEb25nMREwDwYDVQQHDAhTaGVuemhlbjEP
MA0GA1UECgwGSHVhd2VpMQswCQYDVQQLDAJJVDEuMCwGA1UEAwwlSHVhd2VpIFdl
YiBTZWN1cmUgSW50ZXJuZXQgR2F0ZXdheSBDQTAeFw0xNjA1MTAwOTAyMjdaFw0y
NjA1MDgwOTAyMjdaMIGCMQswCQYDVQQGEwJjbjESMBAGA1UECAwJR3VhbmdEb25n
MREwDwYDVQQHDAhTaGVuemhlbjEPMA0GA1UECgwGSHVhd2VpMQswCQYDVQQLDAJJ
VDEuMCwGA1UEAwwlSHVhd2VpIFdlYiBTZWN1cmUgSW50ZXJuZXQgR2F0ZXdheSBD
QTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBANk9kMn2ivB+6Lp23PIX
OaQ3Z7YfXvBH5HfecFOo18b9jC1DhZ3v5URScjzkg8bb616WS00E9oVyvcaGXuL4
Q0ztCwOszF0YwcQlnoLBpAqq6v5kJgXLvGfjx+FKVjFcHVVlVJeJviPgGm4/2FLh
odoreBqPRAfLRuSJ5U+VvgYipKMswTXh7fAK/2LkTf1dpWNvRsoXVm692uFkGuNx
dCdUHYCI5rl6TqMXht/ZINiclroQLkd0gJKhDVmnygEjwAJMMiJ5Z+Tltc6WZoMD
lrjETdpkY6e/qPhzutxDJv5XH9nXN33Eu9VgE1fVEFUGequcFXX7LXSHE1lzFeae
rG0CAwEAAaNQME4wHQYDVR0OBBYEFDB6DZZX4Am+isCoa48e4ZdrAXpsMB8GA1Ud
IwQYMBaAFDB6DZZX4Am+isCoa48e4ZdrAXpsMAwGA1UdEwQFMAMBAf8wDQYJKoZI
hvcNAQELBQADggEBAKN9kSjRX56yw2Ku5Mm3gZu/kQQw+mLkIuJEeDwS6LWjW0Hv
3l3xlv/Uxw4hQmo6OXqQ2OM4dfIJoVYKqiLlBCpXvO/X600rq3UPediEMaXkmM+F
tuJnoPCXmew7QvvQQvwis+0xmhpRPg0N6xIK01vIbAV69TkpwJW3dujlFuRJgSvn
rRab4gVi14x+bUgTb6HCvDH99PhADvXOuI1mk6Kb/JhCNbhRAHezyfLrvimxI0Ky
2KZWitN+M1UWvSYG8jmtDm+/FuA93V1yErRjKj92egCgMlu67lliddt7zzzzqW+U
QLU0ewUmUHQsV5mk62v1e8sRViHBlB2HJ3DU5gE=
-----END CERTIFICATE-----
```
7. 更新证书`sudo update-ca-certificates`
8. 配置代理
   1. `export http_proxy=http://ip:port` 
   2. `export https_proxy=http://ip:port` 

## 闪退报错

在powershell中输入wsl查看输出:

1. 请启用虚拟机平台 Windows 功能并确保在 BIOS 中启用虚拟化
   1. 开启hyper-v模式
   2. 在管理员powershell中执行`bcdedit /set hypervisorlaunchtype auto`
   3. 如果禁用了组策略里面的Device Guard虚拟化安全设置， 需要打开组策略管理
   4. 本地计算机策略 > 计算机配置 > 管理模板>系统 > Device Guard, 打开 基于虚拟化的安全设置为“已开启”或者“未设置”

2. 参考的对象类型不支持尝试的操作
   1.  是使用VPN的原因
   2.  cmd下管理员权限执行`netsh winsock reset`,然后重启即可

## 限制wsl使用的内存

1. 在当前用户目录下新建文件`.wslconfig`
2. 写入内容

```bash
[wsl2]
memory=2048MB # 限制最大使用内存
swap=2G # 限制最大使用虚拟内存
processors=2 # 限制最大使用cpu个数
```
3. 重启wsl2`wsl --shutdown`


# Win10安装Windows Terminal

在Win10应用商店搜索安装

## Windows Terminal美化

```json
"defaults": {
			"backgroundImage": "D:\\wallpaper\\tifa.jpg",
			"backgroundImageOpacity": 0.25,
			"useAcrylic": true,
			"icon": "ms-appx:///ProfileIcons/{9acb9455-ca41-5af7-950f-6bca1bc9722f}.png",
			"backgroundImageStretchMode": "fill",
			"fontFace": "宋体",
			"fontSize": 14,
			"colorScheme": "Solarized Darcula",
			"cursorColor": "#FFFFFF",
			"cursorShape": "bar"
		},
"schemes": [
		{
			"name": "Solarized Dark",
			"black": "#002831",
			"red": "#d11c24",
			"green": "#738a05",
			"yellow": "#a57706",
			"blue": "#2176c7",
			"purple": "#c61c6f",
			"cyan": "#259286",
			"white": "#eae3cb",
			"brightBlack": "#475b62",
			"brightRed": "#bd3613",
			"brightGreen": "#475b62",
			"brightYellow": "#536870",
			"brightBlue": "#708284",
			"brightPurple": "#5956ba",
			"brightCyan": "#819090",
			"brightWhite": "#fcf4dc",
			"background": "#001e27",
			"foreground": "#708284"
		},
		{
			"name": "Solarized Darcula",
			"black": "#25292a",
			"red": "#f24840",
			"green": "#629655",
			"yellow": "#b68800",
			"blue": "#2075c7",
			"purple": "#797fd4",
			"cyan": "#15968d",
			"white": "#d2d8d9",
			"brightBlack": "#25292a",
			"brightRed": "#f24840",
			"brightGreen": "#629655",
			"brightYellow": "#b68800",
			"brightBlue": "#2075c7",
			"brightPurple": "#797fd4",
			"brightCyan": "#15968d",
			"brightWhite": "#d2d8d9",
			"background": "#3d3f41",
			"foreground": "#d2d8d9"
		}
	]
```

## pip安装源修改

在个人用户目录下（即C:\Users\用户名）下找到pip文件夹，没有则需要自己新建一个，然后进入该文件夹，新建一个pip.ini的配置文件，并在该文件内增加以下声明：
```
[global]
trusted-host=cmc-cd-mirror.rnd.huawei.com
index-url=http://cmc-cd-mirror.rnd.huawei.com/pypi/simple/
```