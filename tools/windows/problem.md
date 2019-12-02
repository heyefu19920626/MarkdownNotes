# Windows

- [Windows](#windows)
  - [将快捷方式固定到开始菜单](#将快捷方式固定到开始菜单)
  - [配置无线网卡和有线网卡同时上内外网](#配置无线网卡和有线网卡同时上内外网)
  - [win10添加项目右键VSCode打开](#win10添加项目右键vscode打开)


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