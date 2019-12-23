# Ubuntu

- [..](linux-catalog.md)

- [Ubuntu](#ubuntu)
  - [SSH客户端](#ssh客户端)
  - [V2Ray客户端](#v2ray客户端)
  - [更改登录输入密码界面壁纸](#更改登录输入密码界面壁纸)
  - [创建启动图标并添加到开始菜单](#创建启动图标并添加到开始菜单)
  - [gnome桌面美化](#gnome桌面美化)
    - [重新加载Gnome Shell](#重新加载gnome-shell)
  - [网易云需要root权限才能打开](#网易云需要root权限才能打开)
  - [修改gnome3的快捷键](#修改gnome3的快捷键)
  - [修改win10 + ubuntu18.04启动顺序](#修改win10--ubuntu1804启动顺序)
  - [ubuntu终端显示路径太长](#ubuntu终端显示路径太长)
  - [oh-my-zsh安装与配置](#oh-my-zsh安装与配置)
  - [dpkg解决依赖问题](#dpkg解决依赖问题)
  - [报错](#报错)
  - [ubuntu提示/boot空间不足](#ubuntu提示boot空间不足)
  - [The package *** needs to be reinstalled, but I can't find an archive for it](#the-package--needs-to-be-reinstalled-but-i-cant-find-an-archive-for-it)
  - [Shadowsocks开机自启](#shadowsocks开机自启)

## SSH客户端

1. putty：`apt install putty`
2. FinalShell; [官网](http://www.hostbuf.com/)
   1. `wget www.hostbuf.com/downloads/finalshell_install_linux.sh`
   2. `chmod +x finalshell_install_linux.sh`
   3. `./finalshell_install_linux.sh`
   4. 安装路径`/usr/lib/FinalShell/`
   5. 配置文件路径`/home/$USER/.finalshell/`

## V2Ray客户端

 1. 在https://github.com/v2ray/v2ray-core下载release中的v2ray-linux-64.zip文件
    1. 或者使用``bash<(curl -L -s https://install.direct/go.sh)``直接安装
    2. 最新的好像不支持go.sh安装了
 2. 安装完成的组件
    1. /usr/bin/v2ray/v2ray：V2Ray 程序
    2. /usr/bin/v2ray/v2ctl：V2Ray 工具
    3. /etc/v2ray/config.json：配置文件
    4. /usr/bin/v2ray/geoip.dat：IP 数据文件
    5. /usr/bin/v2ray/geosite.dat：域名数据文件
 3. 通过脚本安装的只支持``systemctl start v2ray``方式启动，停止等
    1. 这样的脚本默认config文件在/etc/v2ray/config.json,要修改服务器等配置直接修改该文件
    2. 配置文件[config.json](../tools/scienceInternet/v2ray/v2ray-config.json)

## 更改登录输入密码界面壁纸

1. 先找一张你自己喜欢的图片，一般大小为1920*1080，格式为jpg或者png都行
2. 将它移动到/usr/share/backgrounds/目录下
   1. ``sudo mv login.jpg /usr/share/backgrounds``
3. 18.04登录背景相关的配置是用css的，配置文件位于/etc/alternatives/gdm3.css,修改之前先备份
4. 修改该文件
```css
#找到默认的这个部分
#lockDialogGroup {
  background: #2c001e url(resource:///org/gnome/shell/theme/noise-texture.png);
  background-repeat: repeat; 
}
#改为
#lockDialogGroup {
  background: #2c001e url(file:///usr/share/backgrounds/login.jpg);         
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center; 
}
```
5. 保存并重启

## 创建启动图标并添加到开始菜单

开始菜单里的图标位置：/usr/share/applications/

1. 创建code.desktop文件
2. 将以下内容写入文件

```
[Desktop Entry] Version=1.0 Type=Application Name=Visual Studio Code Icon=/home/heyefu/tools/vscode/resources/app/resources/linux/code.png Exec="/home/heyefu/tools/vscode/bin/code" %f Comment=Visual Studio Code Categories=Development;IDE; Terminal=false StartupWMClass=code
```

3. 将文件修改为可执行(此步可以不用)
4. 把创建好的.desktop文件复制到/usr/share/applications

## gnome桌面美化

[参考](http://www.r9it.com/20190418/ubuntu-18.04-gnome-optimize.html)

1. 安装 gnome-tweaks
   1. ``sudo apt install gnome-tweak-tool``
2. 在谷歌浏览器中安装扩展[GNOME Shell integration](https://chrome.google.com/webstore/detail/gnome-shell-integration/gphhapmejobijbbhgpjhcjognlahblep)
3. 安装主机连接器
   1. ``sudo apt install chrome-gnome-shell``
4. 推荐扩展
   1. Dash to Panel
   2. Random Walls
   3. TopIcons
   4. User Themes
   5. Workspace Grid
5. 推荐主题与图标
   1. vimix 主题项目: https://github.com/vinceliuice/vimix-gtk-themes
   2. vimix 图标项目: https://github.com/vinceliuice/vimix-icon-theme

### 重新加载Gnome Shell

按住Alt + F2，输入r

## 网易云需要root权限才能打开

[参考](http://www.r9it.com/20190419/neteasy-cloud-music-run-as-root.html)

1. 第一步，修改 /etc/sudoers 文件，在最后面加一行
   1. {user} ALL = NOPASSWD: /usr/bin/netease-cloud-music
   2. 这里的 {user} 就是你当前登录系统的用户，如果不知道请在终端运行 whoami 命令
2. 第二步，sudo vim /usr/share/applications/netease-cloud-music.desktop
3. 修改 Exec=netease-cloud-music %U 为 Exec=sudo netease-cloud-music %U


## 修改gnome3的快捷键

1. 使用命令将所有gnome3快捷命令输出到文件``gsettings list-keys org.gnome.desktop.wm.keybindings | awk ' {printf "%s  %s\n", "gsettings get org.gnome.desktop.wm.keybindings",$1}' > t.sh``
2. 执行１的sh文件，获取所有快捷键``sh t.sh > t.txt``
3. 在t.txt中寻找要修改的快捷键，在t.sh中找到对应的命令
4. 使用命令修改对应快捷的``gsettings set org.gnome.desktop.wm.keybindings switch-to-workspace-right "[]"``为空
5. 使用命令修改切换工作空间的快捷键``gsettings set org.gnome.desktop.wm.keybindings  move-to-workspace-left "['<Super>
   <Control>Left']"``

## 修改win10 + ubuntu18.04启动顺序

1. 修改grub文件``sudo vim /etc/default/grub``  
   1. 更改数字设置默认启动项，想要改为第N项默认就把0改成N-1看到启动界面是第几项N就是几，将数字减去1: ``GRUB_DEFAULT=0``
   2. 选项是设定菜单等待时间，默认为10秒，设置为-1取消倒计时，这个选项也可以一起更改: ``GRUB_TIMEOUT=10``
2. 更新grub.cfg``sudo update-grub``,重启电脑

## ubuntu终端显示路径太长

1. 修改~/.bashrc中的PS1的值，修改后source /home/vagrant/.bashrc或者重启终端
2. PS：一些变量意义
    + \d ：代表日期，格式为weekday month date，例如："Mon Aug 1"
    + \H ：完整的主机名称。名称就是fc4.linux
    + \h ：仅取主机的第一个名字，如上例，则为fc4，.linux则被省略
    + \t ：显示时间为24小时格式，如：HH：MM：SS
    + \T ：显示时间为12小时格式
    + \A ：显示时间为24小时格式：HH：MM
    + \u ：当前用户的账号名称
    + \v ：BASH的版本信息
    + \w ：完整的工作目录名称。家目录会以 ~代替
    + \W ：利用basename取得工作目录名称，所以只会列出最后一个目录
    + \\# ：下达的第几个命令
    + \$ ：提示字符，如果是root时，提示符为：# ，普通用户则为：
    + {debian_chroot:+($debian_chroot)} 这句的意思是说，如果在/etc下有debian_chroot文件，则命令提示符前面就附加上debian_chroot文件的内容

## oh-my-zsh安装与配置

1. 安装：``sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"``
2. 配置终端前缀显示
   1. ``cd  ~/.oh-my-zsh/themes/``
   2. 查看自己主题：``echo $ZSH_THEME``，此处为robbyrussell
   3. ``cp robbyrussell.zsh-theme myrobbyrussell.zsh-theme``
   4. 修改新主题: ``vim myrobbyrussell.zsh-theme``
   5. 修改PROMPT的值
      1. ``PROMPT='%{$fg_bold[red]%}%n -> %{$fg_bold[green]%}%p%{$fg[cyan]%}%1d %{$fg_bold[blue]%}$(git_prompt_info)% %{$reset_color%}~#:'``
      2. %d表示目录，1表示层数
   6. 修改.zshrc中的默认主题 ``vim ~/.zshrc``的ZSH_THEME的值
      1. ``ZSH_THEME="myrobbyrussell"``
   7. 保存退出,启动新终端

## dpkg解决依赖问题

1. dpkg -i　安装包缺少依赖后
2. 更新源``sudo apt-get update``
3. 解决依赖关系``sudo apt-get -f install``
4. 重新安装``sudo dpkg -i ...``

## 报错
1. 切换到root用户的时候很多环境变量不生效
   1. ``vim ~/.zshrc``
   2. 在最前面加入``source $HOME/.bashrc``或者``source /etc/profile``
   3. ``export PATH=$HOME/bin:/usr/local/bin:$PATH``
2. ``/root/.bashrc:13: command not found: shopt``
   1. ``type shopt``
   2. ``echo "#! /bin/bash\n\nshopt \$*\n"> /usr/local/bin/shopt``
   3. ``chmod +x /usr/local/bin/shopt``
   4. ``ln -s /usr/local/bin/shopt /usr/bin/shopt``

## ubuntu提示/boot空间不足
1. 查看安装的内核`dpkg --get-selections |grep linux-`
2. 查看当前运行内核`uname -a`
3. 将旧的内核删除并清理/usr/src文件,  一般以imgage开头
   1. `sudo apt-get purge Linux-image-【版本号】-generic`
   2. `sudo apt-get purge Linux-image-extra-【版本号】-generic`
   3. `sudo apt-get purge Linux-headers-【版本号】-generic`
4. 起码要保留一个，一般就保留最新的内核  
5. `sudo apt-get autoremove`

## The package *** needs to be reinstalled, but I can't find an archive for it

在Ubuntu上卸载软件时遇到题目所述提示，导致无法卸载

1. `$ sudo dpkg --remove --force-all {***，为提示中的软件名称}`
2. `sudo apt-get update`

## Shadowsocks开机自启
1. `ln -fs /lib/systemd/system/rc-local.service /etc/systemd/system/rc-local.service`  
2. `cd /etc/systemd/system/`
3. `cat rc-local.service`
```
#  SPDX-License-Identifier: LGPL-2.1+  
#  
#  This file is part of systemd.  
#  
#  systemd is free software; you can redistribute it and/or modify it  
#  under the terms of the GNU Lesser General Public License as published by  
#  the Free Software Foundation; either version 2.1 of the License, or  
#  (at your option) any later version.  
  
# This unit gets pulled automatically into multi-user.target by  
# systemd-rc-local-generator if /etc/rc.local is executable.  
# [Unit] 区块：启动顺序与依赖关系
[Unit] Description=/etc/rc.local Compatibility Documentation=man:systemd-rc-local-generator(8) ConditionFileIsExecutable=/etc/rc.local After=network.target  
  
[Service]  
# [Service] 区块：启动行为,如何启动，启动类型 Type=forking ExecStart=/etc/rc.local start TimeoutSec=0 RemainAfterExit=yes GuessMainPID=no  
  
[Install]  
# [Install] 区块，定义如何安装这个配置文件，即怎样做到开机启动 WantedBy=multi-user.target Alias=rc-local.service 
```
4. `touch /etc/rc.local`,在rc.local文件中写入以下内容
```bash
#!/bin/bash  

nohup sslocal -c /home/heyefu/configs/shadowsocks/config.json > /home/heyefu/configs/shadowsocks/log 2>&1 &

exit 0
```
5. `chmod 755 /etc/rc.local` 
