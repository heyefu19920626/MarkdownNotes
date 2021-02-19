# Java 工具

- [Java 工具](#java-工具)
  - [CPU调优](#cpu调优)
    - [jps](#jps)
    - [jstack](#jstack)
    - [jstat](#jstat)
    - [jmap](#jmap)
    - [jhat](#jhat)
      - [win下查看进程的线程信息](#win下查看进程的线程信息)
      - [Linux下查看进程的线程信息](#linux下查看进程的线程信息)
      - [报错](#报错)

## CPU调优

1. 查找进程的pid
2. 查找对应pid进程的线程tid信息
3. 将十进制的tid转化为16进制的
4. jstack导出线程堆栈，查看16进制的tid的堆栈信息

### jps

显示当前所有java进程pid

1. `jps -v`输出传递给jvm的参数 

### jstack

1. `jstack -m pid > D:\1.txt`
2. `jstack -l pid > D:\2.txt`

### jstat

### jmap

### jhat

用途：是用来分析java堆的命令，可以将堆中的对象以html的形式显示出来，包括对象的数量，大小等等，并支持对象查询语言

1. 使用jmap导出堆`jmap -dump:live,file=a.hprof`
2. 分析堆文件`jhat a.hprof`
   1. 如果dump的堆很大，空间不足，添加参数`jhat -J-Xmx512m <heap dump file>`
3. 查看html`http://127.0.0.1:7000`
   1. 显示堆中所包含的所有类
   2. 从根集能引用到的对象
   3. 显示平台包括的所有类的实例
   4. 堆实例的分布表
   5. 执行对象查询语句

#### win下查看进程的线程信息

1. pslist,如果没有该命令，到[官网](https://docs.microsoft.com/zh-cn/sysinternals/downloads/pslist)下载后，解压到C:\Windows\System32路径即可
   1. `pslist pid`,查看总线程情况
   2. `pslist -dmx pid`，查看进程的线程详情
   3. 该命令查出的线程tid是十进制的，jstack导出的线程nid(对应tid)是16进制的，可以在[工具网站](https://tool.oschina.net/hexconvert/)转换
2. Process Explorer,到[官网](https://docs.microsoft.com/zh-cn/sysinternals/downloads/process-explorer)下载后直接运行
   1. 找对对应的进程，右键点击Properteis...,切换到Thread选项卡
   2. 找出CPU占用最多的线程tid,同样为十进制

#### Linux下查看进程的线程信息

1.  使用top查看cpu占用最高的进程，并获取其pid
2.  `ps -mp pid -o THREAD,tid,time`,找到cpu占用最高的线程tid
3.  将10进制tid转为16进制，去jstack文件中查看对应的线程

#### 报错
1. 没有以管理员执行
```log
Error attaching to process: Windbg Error: WaitForEvent failed!
sun.jvm.hotspot.debugger.DebuggerException: Windbg Error: WaitForEvent failed!
```
2. 对应位数与版本是否正确
```log
Error attaching to process: Windbg Error: ReadVirtual failed!
sun.jvm.hotspot.debugger.DebuggerException: Windbg Error: ReadVirtual failed!
```