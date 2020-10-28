# Windows10上使用Linux子系统(WSL)

- [..](linux-catalog.md)

- [Windows10上使用Linux子系统(WSL)](#windows10上使用linux子系统wsl)
  - [安装](#安装)
  - [升级至WSL2](#升级至wsl2)
    - [报错处理](#报错处理)
  - [更换阿里源](#更换阿里源)
  - [安装go](#安装go)
  - [使用docker](#使用docker)
  - [闪退报错](#闪退报错)
- [Win10安装Windows Terminal](#win10安装windows-terminal)
  - [Windows Terminal美化](#windows-terminal美化)

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
			"fontFace": "Sarasa Term SC",
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
