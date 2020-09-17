## Win常用命令

- [Win常用命令](#win常用命令)
  - [tasklist](#tasklist)
  - [findStr/findstr](#findstrfindstr)
  - [taskKill](#taskkill)
  - [根据路径查看任务](#根据路径查看任务)
  - [telnet](#telnet)
  - [操作无法完成，已在另一个程序中打开](#操作无法完成已在另一个程序中打开)


### tasklist

> tasklist  
> 查看任务列表  

### findStr/findstr

> tasklist | findStr "xxx"  
> 查找包含xxx的任务

### taskKill

> taskkill -PID 应用pid -F  
> 强制杀死程序

### 根据路径查看任务

> cmd.exe /c wmic process get executablepath,processid | findStr path
> path 为路径

### telnet

> telnet ip port  
> 查看端口连接情况

### 操作无法完成，已在另一个程序中打开

1. 把鼠标放在电脑最下面的任务栏，右击鼠标，选择”启动任务管理器“，如下图所示：
2. 打开”启动任务管理器“，选择上面菜单”性能“里的”资源监视器“，如下图所示：
3. 打开”资源监视器“里菜单”CPU“,选择下面的”关联的句柄“里的搜索框。所图所示：
3. 在”关联的句柄“里的搜索框里输入你要打开的文件夹名称。比如”合同样本”，会跳出相关的进程，如下图所示：
4. 右击关联的进程，并选择结束进程，把所有的关联进程都结束后，再对文件或文件夹进行操作就可以了。