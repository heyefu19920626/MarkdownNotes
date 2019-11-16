# Windows10上使用Linux子系统(WSL)

### 安装

- 开启开发者模式
    + 设置->更新和安全->开发者选项->开发人员模式
- 在Microsoft store安装Uubuntu 18.04LTS
- 在启用或关闭windows功能勾选适用于Linux的Windows子系统

### 升级至WSL2

- 加入预览版体验计划,更新到最新系统
- 管理员运行在powershell中执行
```
#启用虚拟机平台可选组件
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
#查看当前版本
wsl -l -v
#升级
wsl --set-version Uubuntu-18.04 2
```

### 更换阿里源

- 备份/etc/apk/source.list,编辑并替换为阿里源

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

### 安装go

- 略
- root用户每次都要重新source /etc/profile
    + 在~/.bashrc或~/.profile中添加环境变量

### 使用docker
> export DOCKER_HOST=tcp://localhost:2375

对于运行在WSL中的Docker而言，它会采用类似/mnt/c/Users/Payne/这样的更符合Linux习惯的路径，而Docker for Windows则会使用类似/c/Users/Payne/这样更符合Windows习惯的路径。因此，如果你在使用Docker的过程中，需要处理分区挂载相关的东西，一个比较好的建议是修改WSL的配置文件(如果不存在需要自行创建)
```
sudo nano /etc/wsl.conf
[automount]
root = /
options = "metadata"
```
