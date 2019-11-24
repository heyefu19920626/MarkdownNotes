# Ubuntu


- [Ubuntu](#ubuntu)
  - [修改win10 + ubuntu18.04启动顺序](#修改win10--ubuntu1804启动顺序)
  - [ubuntu终端显示路径太长](#ubuntu终端显示路径太长)
  - [oh-my-zsh安装与配置](#oh-my-zsh安装与配置)
  - [dpkg解决依赖问题](#dpkg解决依赖问题)
    - [报错](#报错)

## 修改win10 + ubuntu18.04启动顺序
- 修改grub文件``sudo vim /etc/default/grub``
```
#更改数字设置默认启动项，想要改为第N项默认就把0改成N-1看到启动界面是第几项N就是几，将数字减去1
GRUB_DEFAULT=0
#选项是设定菜单等待时间，默认为10秒，设置为-1取消倒计时，这个选项也可以一起更改。
GRUB_TIMEOUT=10
```
- 更新grub.cfg``sudo update-grub``,重启电脑

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

### 报错
1. 切换到root用户的时候很多环境变量不生效
   1. ``vim ~/.zshrc``
   2. 在最前面加入``source $HOME/.bashrc``或者``source /etc/profile``
   3. ``export PATH=$HOME/bin:/usr/local/bin:$PATH``
2. ``/root/.bashrc:13: command not found: shopt``
   1. ``type shopt``
   2. ``echo "#! /bin/bash\n\nshopt \$*\n" > /usr/local/bin/shopt``
   3. ``chmod +x /usr/local/bin/shopt``
   4. ``ln -s /usr/local/bin/shopt /usr/bin/shopt``