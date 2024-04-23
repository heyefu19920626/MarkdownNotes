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