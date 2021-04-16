# 常用命令

- [..](linux-catalog.md)

- [常用命令](#常用命令)
  - [4种将前一命令结果作为后一命令参数](#4种将前一命令结果作为后一命令参数)
    - [注意](#注意)
  - [strace](#strace)
  - [批量重命名](#批量重命名)
  - [ls](#ls)
  - [tail](#tail)
  - [netstat](#netstat)
  - [find](#find)
  - [xargs与exec,ok](#xargs与execok)
  - [dhclient](#dhclient)
  - [chown](#chown)
  - [chomd](#chomd)
  - [查看CPU配置](#查看cpu配置)
  - [查看网络状况](#查看网络状况)
  - [后台运行](#后台运行)
  - [统计文件个数](#统计文件个数)
  - [chrome 网页截图](#chrome-网页截图)
  - [文件编码格式转换](#文件编码格式转换)
  - [expect自动发送用户名密码](#expect自动发送用户名密码)
  - [输出重定向](#输出重定向)
  - [grep](#grep)
  - [nsenter,在docker中执行curl命令](#nsenter在docker中执行curl命令)
  - [bash脚本，循环](#bash脚本循环)
  - [用户相关](#用户相关)


很多进程信息在/proc目录下

## 4种将前一命令结果作为后一命令参数

1. find / -name “test*” |xargs rm -rf 
2. find / -name “test*” -exec rm -rf {} \; 
3. rm -rf $(find / -name “test”)
4. docker images|awk '$2 ~ /^test$/ {print $1":"$2}'|xargs docker rmi
5. grep -E "regex"  (grep使用正则)

### 注意

1. exec 最后的大括号要与前面的空一格，最后的分号不能少

## strace

## 批量重命名
1. 使用脚本
```bash
#!/bin/bash

#http://blog.csdn.net/longxibendi
ls | awk 'gsub(/.html/,""){print $1}'|while read i
#find ./ -name "*.html"  |  while read i
do
    echo "$i";
    mv $i.html  $i.jpg
done
ls | while read i
do
# 将awk替换后的值赋给shell变量
   name=`echo $name | awk 'gsub(/.[0-9]+/,".mp4"){print $0}'`
#   输出mv命令，执行脚本时将命令输出到文件后再用bash执行
   echo mv \'$i\' \'$name\'
#   直接使用命令会报错，不知为啥
#    mv \'$i\' \'$name\'
done
```

```
ls | awk 'gsub(/.html/,""){print $1}'|while read i
do
    echo "$i";
    mv $i.html  $i.jpg
done
```

- awk
  - gsub 替换


## ls
用来显示目标列表

- >ls -l 文件名，文件类型、权限模式、硬连接数、所有者、组、文件大小和文件的最后修改时间
- ll按时间排序
    - >ll -t 降序
    - >ll -t|tac 升序
- ls
    - > -s 列出大小
    - > -S 按大小降序
    - > -r 反转
> ls -l --time-style='+%Y-%m-%d %H-%M-%S'

## tail
用于列出输入文件中的尾部内容,默认显示最后10行

- >-f 显示文件最新追加内容
- >-c <N>  输出文件尾部N个字节的内容,N可以有后缀b(512字节),k,m
- >tail -n +20 file 输出从第20行至末尾的内容

## netstat
用来打印Linux中网络系统的状态信息

- > -a或--all 显示所有连线中的Socket
- > -n或--numeric：直接使用ip地址，而不通过域名服务器
- > -p或--programs：显示正在使用Socket的程序识别码和程序名称

## find
用来在指定目录下查找文件

linux中文件的时间有三种:
> mtime：文件最后内容修改时间。  
> ctime：文件最后属性改变时间，也可以叫status。可以使用-c代替。  
> atime：文件最后被读取时间，也可以叫access，use。可使用-u代替。

- > find -type f -ctime -1|xargs ls -l 列出1天内最后属性改变的文件
- > find -type f -mtime +5 -exec ls -l {} \\; 列出5分钟以前内容修改的文件
- 时间之前不带符号刚好5天之前的文件
- exec 可以换成ok,会给出提示
```
注意一定是{} \;别漏掉了分号。{}是指find . -type f -mtime +5的返回的结果，\;是指命令尾部标记
```

## xargs与exec,ok
- exec对每一个结果执行一次命令
- xargs全部塞给命令
- xargs可以通过-n来控制每次传递参数的个数

## dhclient
- 动态获取IP

## chown
改变某个文件或目录的所有者和所属的组
参数

- 用户：组：指定所有者和所属工作组。当省略“：组”，仅改变文件所有者
- 文件：指定要改变所有者和工作组的文件列表。支持多个文件和目标，支持shell通配符
> -R 递归处理

## chomd
- 改变权限
- 权限数字原则
  - 权限数组为3位数，首位表示所属者的权限，中位表示所属组的权限，末位表示其他人的权限
  - 可读为4，可写为2，可执行为1,权限数字为所有权限相加
```
其中a,b,c各为一个数字，a表示User，b表示Group，c表示Other的权限。
r=4，w=2，x=1
若要rwx（可读、可写、可执行）属性，则4+2+1=7
若要rw-（可读、可写、不可执行）属性，则4+2=6
若要r-w（可读、不可写、可执行）属性，则4+1=5
```

## 查看CPU配置
> ``lscpu``

## 查看网络状况
> ``iftop``  
> -i 网卡信息

## 后台运行

1. `Ctrl + Z`将当前任务切换到后台挂起
2. `jobs`列出当前shell环境中已启动的任务状态，若未指定jobsid，则显示所有活动的任务状态信息
3. `fg %jobid`将后台任务切换到前台，参数省略时切换最后一个后台任务到前台
4. `bg %jobid`将一个后台暂停的命令继续运行
5. `&`在命令的最后追加这个符号，让命令在后台运行
6. `stop %jobid`或者`kill -stop pid`(redhat不存在stop)将进程挂起
7. `kill %jobid`杀死后台job进程(杀死进程为`kill pid`)

## 统计文件个数

1. `ls -l | grep '^-' | wc -l`
   1. ls -l 列出文件
   2. grep '^-'查找以-开头的行数
   3. wc -l统计行数 

## chrome 网页截图

1. F12
2. Ctrl+Shift+P,搜索命令
3. 输入screen搜索截图相关命令
   1. 选择Capture full size screenshot

## 文件编码格式转换

1. 查看文件格式
   1. `vim target`,target为目标文件
   2. 命令模式`:set fileencoding`查看文件格式
   3. `set fenc`,`set enc`
2. 查看文本模式类型
   1. `set ff`,可以设置文本模式，dos，unix等
3. 查看文件编码
   1. `file target`,在命令行直接使用，target为目标文件
4. 设置文件格式
   1. vim
      1. `set fileencoding=utf-8`
   2. iconv
      1. `iconv -f gbk -t utf-8 fromfile`将gbk格式的fromfile文件转换为utf-8格式的文件并输出到命令行

## expect自动发送用户名密码

1. which expect查看是有该命令，没有，则安装
2. 编写脚本
3. 执行
   1. 有时可以使用nohup  ... &执行

```bash
#!/bin/expect  
#修改执行程序为expect

#设置延时为1000秒
set timeout 1000

#判断参数个数
if { [llength $argv] < 1} {
   puts "miss argument"
   puts "1: exe"
   puts "2: green"
   exit 1
}
set target_file [lindex $argv 0]
puts "target file: $target_file"

#判断等于
if { "$target_file" == "1" } {
   set target_file "exe.zip_1"
} else {
   set target_file "green.zip_1"
}
puts "target file: $target_file"

#spawn是expect的语句，执行命令前都要加这句
spawn ftp osaftp.his.huawei.com

expect {
  "*):*" {
    # 会覆盖上面的timeout
    set timeout 1000
    #\r表示回车
    send "admin\r"
    #输入用户后继续匹配
    exp_continue
  }
  "*assword:*" {
    set timeout 1000
    send "admin\r"
  }
  timeout {
    puts "connect is timeout"
    exit 3
  }
}
expect "ftp*"
send "bin\r"
expect "ftp*"
send "get $target_file\r"
#使用nohup时可能需要多留几行？
#expect "ftp*"
#interact代表执行完留在远程控制台
interact
```

## 输出重定向

1. `>`: 覆盖重定向
2. `>>`: 追加重定向
3. `命令 >> 文件 2>&1`: 把正确输出和错误输出都追加到同一个文
4. `命令>> 文件1 2>>文件2`: 把正确输出追加到文件1，把错误输出追加到文件2
5. `echo -e >> 文件`: 向文件中输出换行

## grep

1. 正则`grep '^[^0]'`, 搜索非0开头的行
2. 统计行数
   1. `grep -c keyword 文件1 文件2`: 分别返回每个文件中匹配keyword的个数
   2. `wc -l`

## nsenter,在docker中执行curl命令

nsenter命令是一个可以在指定进程的命令空间下运行指定程序的命令

有的docker容器没有curl命令，因此借助nsenter在外部执行  
1. `docker inspect 容器id | grep Pid`: 搜索容器的Pid
2. `nsenter -t Pid -n curl -o /dev/null -s -w -k '%{time_connect} %{time_starttransfer} %{time_total}' "http://10.254.149.31:8000/"`,在主机中使用nsenter在容器内部执行

## bash脚本，循环

1. Shell中判断两个数字大小的方式：
   1. `-gt（大于）  -lt（小于）  -eq（等于）  -le（小于等于）  -ge（大于等于）`
2. 判断两个字符串的方式：
   1. `>（大于）    <（小于）   ==（等于）   >=（大于等于）   <=（小于等于）`

```bash
#! /bin/bash

# 定义变量i, i默认是字符串，变量与等号间不能有空格
i=1

# 表达式用中括号括起来，与中括号间必须有空格
while [ $i -lt 10 ]
do
#        nsenter -t 29194 -n curl -o /dev/null -s -w '%{time_connect} %{time_starttransfer}' https://10.247.203.18:31003 -k >> test.time
        curl -o /dev/null -s -w '%{time_connect} %{time_starttransfer}' https://10.247.203.18:31003 -k >> test.time
        echo -e >> test.time
      #   i++, 双括号(())即数学计算表达式
        (( i++ ))
done
# if elif else
if [ $i -lt 15 ]
then
   echo "i < 15"
elif [ $i -eq 15 ]
then
   echo "i == 15"
else
   echho "i > 15"
# if结束语
fi
# 也可以循环字符串 for loop in "abc" "def"
for loop in 1 2 3
# do相当于左大括号
do
   echo $loop
# done相当于左大括号
done

for ((i=0;i<10;i++))
do
   echo "i=$i"
done

seq是一个命令，1表示i的初值，2表示i的步长，10表示i的最大值
for i in $(seq 1 2 10)
do
   echo "i=${i}abc"
done

# until与其他循环不同，它是判断表达是为假时进行循环
until [ $i -gt 10 ]
do
   echo "i = $i"
   ((i++))
done
```

## 用户相关

1. useradd
   1. `useradd -m test`,创建test用户，并创建test家目录
2. passwd
   1. `passwd test`，给test用户设置密码
3. userdel
   1. `userdel -r test`,删除test用户，并删除test所在目录
4. usermod
   1. `usermod -s /bin/bash test`,给test用户设置shell为/bin/bash