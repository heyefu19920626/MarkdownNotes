# Win常用命令

- [Win常用命令](#win常用命令)
  - [tasklist](#tasklist)
  - [findStr/findstr](#findstrfindstr)
  - [taskKill](#taskkill)
  - [根据路径查看任务](#根据路径查看任务)
  - [telnet](#telnet)
  - [查看端口占用](#查看端口占用)
  - [设置环境变量](#设置环境变量)
    - [set与setx](#set与setx)
    - [winc](#winc)
    - [示例](#示例)
  - [操作无法完成，已在另一个程序中打开](#操作无法完成已在另一个程序中打开)
  - [任务栏无法自动隐藏](#任务栏无法自动隐藏)
  - [查看当前路径](#查看当前路径)
  - [删除文件](#删除文件)


## tasklist

> tasklist  
> 查看任务列表  

## findStr/findstr

> tasklist | findStr "xxx"  
> 查找包含xxx的任务

## taskKill

> taskkill -PID 应用pid -F  
> 强制杀死程序

## 根据路径查看任务

> cmd.exe /c wmic process get executablepath,processid | findStr path
> path 为路径

## telnet

> telnet ip port  
> 查看端口连接情况

## 查看端口占用

> netstat -ano|findstr port

## 设置环境变量

设置环境变量以持续时间分为临时和永久，以用户分为用户和系统

### set与setx

使用set PATH查看当前环境变量,PATH代表变量名

1. 临时设置
```bat
set java_home=C:\tools\java
set PATH="%java_home%;%PATH%"
Pause
```
2. 永久设置用户环境变量
```bat
set java_home=C:\tools\java
setx PATH "%java_home%;%PATH%"
```
3. 永久设置系统环境变量
```bat
set java_home=C:\tools\java
setx PATH "%java_home%;%PATH%" /m
```

### winc

1. 查询系统环境变量
> wmic ENVIRONMENT where "name='PATH' and username='<system>'"  
> wmic ENVIRONMENT where "name='PATH' and username='<system>'"  get VariableValue
2. 更新系统环境变量
> wmic ENVIRONMENT where "name='PATH' and username='<system>'"  set VariableValue="C:\tools\java;%PATH%"
3. 创建环境变量
> wmic ENVIRONMENT create name="JAVA_HOME",username="<system>",VariableValue="%javaPath%"
4. 删除环境变量
> wmic ENVIRONMENT where "name='JAVA_HOME'" delete

### 示例
```bat
@echo off

:: TODO:设置java环境变量
:: Author: Gwt
color 02
::设置java的安装路径，可方便切换不同的版本
set input=
set /p "input=请输入java的jdk路径（或回车默认路径为C:\Program Files\Java\jdk1.7.0_71）:"
if defined input (echo jdk已设置) else (set input=C:\Program Files\Java\jdk1.7.0_71)
echo jdk路径为%input%
set javaPath=%input%

::如果有的话，先删除JAVA_HOME
wmic ENVIRONMENT where "name='JAVA_HOME'" delete

::如果有的话，先删除ClASS_PATH
wmic ENVIRONMENT where "name='CLASS_PATH'" delete

::创建JAVA_HOME
wmic ENVIRONMENT create name="JAVA_HOME",username="<system>",VariableValue="%javaPath%"

::创建CLASS_PATH
wmic ENVIRONMENT create name="CLASS_PATH",username="<system>",VariableValue=".;%%JAVA_HOME%%\lib\tools.jar;%

%JAVA_HOME%%\lib\dt.jar;"

::在环境变量path中，剔除掉变量java_home中的字符，回显剩下的字符串
call set xx=%Path%;%JAVA_HOME%\jre\bin;%JAVA_HOME%\bin

::echo %xx%

::将返回显的字符重新赋值到path中
wmic ENVIRONMENT where "name='Path' and username='<system>'" set VariableValue="%xx%"

pause
::@echo off 是关闭回显的，不会显示命令信息 on打开会显示命令信息
::color 02是设置输出文本颜色的，这里是控制命令台输出绿颜色
::set /p "input=请输入命令信息" 是用来接收控制台输入的文本信息的
::if else 是用来做判断 if defined input 是用来判断用户是否输入信息，回车的话，则表示未定义input的值
::echo "输出信息" 是用来显示信息的
::set javaPath=%input% 是用来吧变量input的值赋值给javaPath变量的
```

## 操作无法完成，已在另一个程序中打开

1. 把鼠标放在电脑最下面的任务栏，右击鼠标，选择”启动任务管理器“，如下图所示：
2. 打开”启动任务管理器“，选择上面菜单”性能“里的”资源监视器“，如下图所示：
3. 打开”资源监视器“里菜单”CPU“,选择下面的”关联的句柄“里的搜索框。所图所示：
3. 在”关联的句柄“里的搜索框里输入你要打开的文件夹名称。比如”合同样本”，会跳出相关的进程，如下图所示：
4. 右击关联的进程，并选择结束进程，把所有的关联进程都结束后，再对文件或文件夹进行操作就可以了。

##  任务栏无法自动隐藏

通过任务管理器重启Windows资源管理器

## 查看当前路径

1. %cd%
   1. 实时变化，末尾不带\
2. %~dp0
   1. 只能用于批处理文件中，末尾带\,标识批处理文件所在目录

## 删除文件

```bat
DEL /F /A /Q \\?\%1
RD /S /Q \\?\%1
```
