## 修改win10 + ubuntu18.04启动顺序
- 修改grub文件
> sudo vim /etc/default/grub

```
#更改数字设置默认启动项，想要改为第N项默认就把0改成N-1看到启动界面是第几项N就是几，将数字减去1
GRUB_DEFAULT=0
#选项是设定菜单等待时间，默认为10秒，设置为-1取消倒计时，这个选项也可以一起更改。
GRUB_TIMEOUT=10
```
- 更新grub.cfg,重启
> sudo update-grub

## ubuntu终端显示路径太长

- 修改~/.bashrc中的PS1的值，修改后source /home/vagrant/.bashrc或者重启终端
- PS：一些变量意义
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