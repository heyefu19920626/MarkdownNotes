# Windows

- [Windows](#windows)
	- [启用或关闭Windows功能](#启用或关闭windows功能)
	- [Windows上帝模式](#windows上帝模式)
	- [禁用svchost](#禁用svchost)
	- [将快捷方式固定到开始菜单](#将快捷方式固定到开始菜单)
	- [配置无线网卡和有线网卡同时上内外网](#配置无线网卡和有线网卡同时上内外网)
	- [win10添加项目右键VSCode打开](#win10添加项目右键vscode打开)
	- [Microsoft软件保护平台服务](#microsoft软件保护平台服务)
	- [如何查看激活](#如何查看激活)
	- [怎么设置nginx开机启动](#怎么设置nginx开机启动)
	- [cmd窗口中文乱码](#cmd窗口中文乱码)
	- [路由](#路由)

## 启用或关闭Windows功能


## Windows上帝模式

1. 在桌面新建一个文件夹
2. 重命名为`GodMode.{ED7BA470-8E54-465E-825C-99712043E01C}`


## 禁用svchost

1. 在服务中找到Background Intelligent Transfer Service
2. 启动类型改为禁用,服务状态改为停止服务


## 将快捷方式固定到开始菜单

将自己的快捷方式拖到``C:\ProgramData\Microsoft\Windows\Start Menu\Programs``


## 配置无线网卡和有线网卡同时上内外网
[参考CSDN博客](https://blog.csdn.net/qq_33819574/article/details/81026539)

使用CMD:

- route print
    - 输出当前的路由配置
    - 网络目标中的0.0.0.0表示所有的访问都使用这个网卡
- route delete 0.0.0.0 mask 0.0.0.0
    + 删除所有访问0.0.0.0的路由配置
- route add <font color="red">0.0.0.0</font> mask 0.0.0.0 <font color="red">192.168.43.1</font>
    + 添加无线路由访问外网
    + 第一个0.0.0.0是网络目标，第二个0.0.0.0是网络掩码，第三个192.168.43.1是wifi网关
- route add 192.168.101.1 mask 255.255.255.0 192.168.101.1 
    + 添加有线路由访问内网
    + 第一个192.168.101.1是网络目标，也就是你要访问的内网的网关，第二个255.255.255.0是网络掩码，第三个192.168.101.1是你的有线网络网关

## win10添加项目右键VSCode打开

[参考CSDN博客](https://blog.csdn.net/qq_31424825/article/details/84325084)

1. 在VSCode安装目录下新建reg文件，文件名任意,例如:add_vscode.reg
2. 编写文本文件内容.将下面的内容Copy到刚才新建的*.reg文件中,注意替换其中的VSCode安装路径
```reg
	Windows Registry Editor Version 5.00
	
	[HKEY_CLASSES_ROOT\*\shell\VSCode]
	@="Open with Code"
	"Icon"="D:\\tools\\VSCode\\Code.exe"
	
	[HKEY_CLASSES_ROOT\*\shell\VSCode\command]
	@="\"D:\\tools\\VSCode\\Code.exe\" \"%1\""
	
	Windows Registry Editor Version 5.00
	
	[HKEY_CLASSES_ROOT\Directory\shell\VSCode]
	@="Open with Code"
	"Icon"="D:\\tools\\VSCode\\Code.exe"
	
	[HKEY_CLASSES_ROOT\Directory\shell\VSCode\command]
	@="\"D:\\tools\\VSCode\\Code.exe\" \"%V\""
	
	Windows Registry Editor Version 5.00
	
	[HKEY_CLASSES_ROOT\Directory\Background\shell\VSCode]
	@="Open with Code"
	"Icon"="D:\\tools\\VSCode\\Code.exe"
	
	[HKEY_CLASSES_ROOT\Directory\Background\shell\VSCode\command]
	@="\"D:\\tools\\VSCode\\Code.exe\" \"%V\"" 
```
3. 保存后，双击运行，确认即可

## Microsoft软件保护平台服务

据说kms激活不完整导致的

1. 在运行中输入`regedit`,编辑注册表
2. 找到`计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\sppsvc`
3. 修改右侧的start的值为4(原先为2)
4. 重启

按以上方式修改后会导致错误0xc0020036,将值改回去重启就能修复

## 如何查看激活

1. 管理员运行命令行
   1. `slmgr.vbs -xpr`可查看是否永久激活
   2. `slmgr.vbs -dlv`查看激活状态

## 怎么设置nginx开机启动

1. 按下 Win + R 组合键打开运行对话框
2. 输入 shell:startup，然后点击确定，这将打开 Windows 的启动文件夹
3. 在启动文件夹中，创建一个快捷方式，指向 Nginx 的安装目录中的 nginx.exe 文件
4. 将该快捷方式移动到启动文件夹中

## cmd窗口中文乱码

1. 临时解决，执行`chcp 65001`
2. 永久解决
   1. win+r运行,输入"regedit",找到HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor
   2. 然后“右键-新建”，选择“字符串值”，“名称”列填写“autorun”, 数值数据填写“chcp 65001”
3. 注意：这样解决了显示时的中文乱码问题，但cmd输入中文会乱码

## 路由

有时候我们需要同时连接有线网和无线网，有线网用来接内部运维网络，无线网用来连接互联网络,这时如果与内部运维网络不通，可能是由于路由不正确造成的, 需要手动添加或者删除路由，来与内部网络连接  

比如我这里一直`ping 10.120.47.1`不通，但`ping 10.120.47.5`是通的

1. 使用`route print`查看当前路由, 看看目标网络是走的哪条路由, 如下，结果发现是走的不是我们期待的网关
```cmd
10.120.47.1  255.255.255.255    10.16.127.254    10.16.124.229     55
```
2. 使用`route delte 10.120.47.1 mask 255.255.255.255`删除这条路由，如果删除不掉，可能这条路由是有DHCP自动分配的，断掉wifi，重新连接WiFi即可刷新(可能还需要开启WIFI每次连接随机硬件地址，否则即时重新连接，仍然会有这条路由)
3. 使用`route add 10.120.47.0 mask 255.255.255.0 10.120.43.254`添加新路由，命令格式为:
```cmd
route add <目标网络> mask <子网掩码> <网关> metric <跃点数> if <接口索引>
<目标网络>：目标网络的IP地址。
<目标网络>：目标网络的IP地址。
mask <子网掩码>：目标网络的子网掩码。
<网关>：下一跳网关的IP地址。
metric <跃点数>：路由的跃点数（可选）。
if <接口索引>：指定使用的网络接口索引（可选）。
```
4. route添加的是临时的，重启会消失，要持久化，可以使用-p选项，`route -p add 192.168.2.0 mask 255.255.255.0 192.168.1.1`