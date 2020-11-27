# Mysql

- [..](database-catalog.md)


- [Mysql](#mysql)
  - [update做了什么](#update做了什么)
  - [Mariadb修改密码](#mariadb修改密码)
    - [知道密码](#知道密码)
    - [忘记密码](#忘记密码)
  - [Mariadb允许远程访问](#mariadb允许远程访问)
  - [导入与导出数据库文件](#导入与导出数据库文件)
    - [导出](#导出)
    - [导入](#导入)
  - [Mysql中创建日期和更新日期自动更新](#mysql中创建日期和更新日期自动更新)
  - [datetime与timestamp](#datetime与timestamp)
  - [Win10安装Maridb](#win10安装maridb)
  - [Centos7 卸载自带的MaraiDB](#centos7-卸载自带的maraidb)
  - [时区设置](#时区设置)
  - [排序规则](#排序规则)
  - [创建新用户](#创建新用户)
  - [授权](#授权)
  - [修改文件存储位置](#修改文件存储位置)

## update做了什么

1. 开启事务，将原内容写入undo log
2. 去Buffer Pool 中 查找id =2 所对应的数据
3. 如果在Buffer Pool中查找到了对应的数据，那么直接在Buffer Pool 中直接修改对应数据
4. 如果没有找到，那么先从磁盘中找到对应数据，然后加载到Buffer Pool 中进行修改
5. 将更新的内容写入redo log
6. 如果开启了binlog ，还会写入binlog日志
7. 事务提交进入prepare 阶段，将redo log 刷入磁盘
8. 事务提交进入commit 阶段， 将binlog 输入磁盘


## Mariadb修改密码

### 知道密码

先进入mariadb控制台
1. 登入数据库修改,直接修改数据库字段
   1. `use mysql;`
   2. `UPDATE user SET password=password('newpassword') WHERE user='root';`
   3. `flush privileges;`
   4. `exit`
2. 不登入数据库
   1. `SET password for 'root'@'localhost'=password('newpassword');`
   2. `exit`
3. 直接在命令行使用mysqladmin
   1. mysqladmin -u用户名 -p旧密码 password 新密码
   2. `mysqladmin -uroot -p123456 password root`


### 忘记密码

1. 停止mariadb: `systemctl stop mariadb`
2. 以跳过权限方式启动mariadb: `/usr/bin/mysqld_safe --user=mysql --skip-grant-tables &`
3. 在命令行进入mysql: `mysql`
4. 切换至mysql库: `use mysql;`
5. 修改root的密码: `update user set password=password('root') where user='root';`
   1. mysql8以上版本需要使用新的命令`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'yourpassword';`
   2. 或者`alter user 'root'@'localhost' identified by 'newpassword';`
6. 退出数据库: `exit`
7. 停止mariadb跳过权限的启动进程,查看进程并停止进程(先停止root账户的进程再停止mysql的进程)
   1. `ps aux |grep mysql`
   2. `kill -9 4356`

## Mariadb允许远程访问

```bash
mysql -uroot -p
 
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;  
注：root是登陆数据库的用户，123456是登陆数据库的密码，*就是意味着任何来源任何主机
mysql> FLUSH PRIVILEGES; 刷新使之生效
mysql> quit
service mysqld restart 重新启动
```

或者修改库mysql的user表的host，
1. `select user, host, passowrd from mysql.user;`
2. 将需要远程连接的账户的host改为%,密码改为自己的密码
   1. `update user set host='%', password=password('root') where host='127.0.0.1' and user='root';`
3. 刷新权限: `FLUSH PRIVILEGES;` 刷新使之生效

## 导入与导出数据库文件

### 导出
1. 导出数据库与文件
   1. mysqldump -u 用户名 -p 数据库名 > 文件名.sql: `mysqldump -u root -p boot_template > boot.sql`
2. 只导出结构
   1. mysqldump -u 用户名 -p -d 数据库名 > 文件名.sql: `mysqldump -u root -p -d boot_template > boot_stru.sql`

### 导入
1. 在mysql控制台导入
   1. 首先建空数据库: `mysql>create database abc;`
   2. 选择数据库: `use abc;`
   3. 设置数据库编码: `set names utf8;`
   4. 导入数据（注意sql文件的路径）: `source /home/heyefu/Downloads/abc.sql;`
2. 在终端导入
   1. mysql -u 用户名 -p 数据库名< 数据库名.sql: `mysql -u root -p abc < abc.sql`
   2. 然后输入密码即可

## Mysql中创建日期和更新日期自动更新

1. 创建时间字段
    > `creat_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP  

2. 更新时间字段
    >  `update_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

3. navcat中
    + 设置默认值为``CURRENT_TIMESTAMP``
    + 勾选not null
4. 当Mysql字段类型设置为timestamp，且默认值设置为CURRENT_TIMESTAMP，并不为空时，如果插入时设置该字段为NULL则报错
    > 修改mysql的配置文件my.ini中将explicit_defaults_for_timestamp=true注释或者改为fales

## datetime与timestamp

1. datetime占用8个字节，而timestamp占用4个字节。
2. 还有就是表示的时间范围不同。
3. timestamp的时间显示值依赖于时区；如果在多个时区存储或访问数据，timestamp和datetime的行为将很不一样。timestamp提供的数值和时区有关系，而datetime则是文本表示的日期和时间。
4. 除了特殊的行为，通常也应该尽量使用TIMESTAMP。
```sql
create table tb(
t1 datetime default current_timestamp on update current_timestamp,     #设置为当前时间戳为默认值，并且自动更新    
t2 datetime default current_timestamp,                                 #仅设置当前时间戳为默认值
t3 timestamp default current_timestamp on update current_timestamp,
t4 timestamp default current_timestamp,
t5 varchar(10),
t6 int auto_increment not null primary key)
```

## Win10安装Maridb

1. 在[官网](https://mariadb.com/downloads/)下载对应安装包
2. 直接安装即可
3. 如果出现错误`Faild to start,Verify that you have suffcient privileges to start system services.`
   1. 权限问题
   2. 开始菜单搜索:服务，找到MariDB，右键打开属性，切换到登录tab标签，登录身份修改为本地系统账户,应用即可
4. maridb10.4
   1. mysql.user表已经退役。用户帐户和全局权限现在存储在mysql.global_priv表


## Centos7 卸载自带的MaraiDB

1. 查看已安装的包： `rpm -qa | grep mariadb`
```
mariadb-5.5.64-1.el7.x86_64
mariadb-server-5.5.64-1.el7.x86_64
mariadb-libs-5.5.64-1.el7.x86_64
```
2. 依次卸载
   1. rpm -e --nodeps mariadb-libs-5.5.64-1.el7.x86_64
安装最新的Maraidb可以去官网查看[yum安装方法](https://mariadb.com/kb/en/mariadb-package-repository-setup-and-usage/)

## 时区设置

1. 控制台进入mysql
2. `show variables like'%time_zone'; `查看时区
3. `set global time_zone = '+8:00'; `设置时区

## 排序规则

mysql默认varchar类型是对大小写不敏感（不区分），如果想要mysql区分大小写需要设置排序规则：
1. utf8_bin将字符串中的每一个字符用二进制数据存储，区分大小写
2. utf8_genera_ci不区分大小写，ci为case insensitive的缩写，即大小写不敏感
3. utf8_general_cs区分大小写，cs为case sensitive的缩写，即大小写敏感


utf8_general_cs这个选项一般没有，所以只能用utf8_bin区分大小写
1. 设置排序规则是可逆的，如果之前设置的排序规则不符合，更换排序规则后，可能出现乱码，当再次恢复原来的排序规则后，乱码即消失
2. 可以将varchar 类型改为 varbinary
3. 如果已经使用了默认的排序规则，即utf8_genera_ci，而又想查询结果大小写区分，可以在查询时进行限定：select binary column from table;   或者  select column2 from table where binary cloumn;

## 创建新用户

1. 创建用户
> create user 'heyefu'@'%' identified by 'password';  
2. 刷新权限
> flush privileges;  
3. 修改密码
> alter user ‘heyefu'@'%' identified by 'newpassword';  
4. 删除用户
> drop user 'test1'@'localhost';

## 授权

1. 授权`grant all privileges on *.* to 'test1'@'localhost' with grant option;`
   1. with gran option表示该用户可给其它用户赋予权限，但不可能超过该用户已有的权限
   2. all privileges 可换成select,update,insert,delete,drop,create等操作
   3. 第一个*表示通配数据库，可指定新建用户只可操作的数据库
   4. 第二个*表示通配表，可指定新建用户只可操作的数据库下的某个表
   5. 库名和表名有特殊符号时使用`database-name`包起来
2. 查看用户授权信息`show grants for 'test1'@'localhost';`
3. 撤销授权`revoke all privileges on *.* from 'test1'@'localhost';`
   1. 用户有什么权限就撤销什么权限
   2. 撤销授权报错` you need (at least one of) the SYSTEM_USER privilege(s) for this operation`
      1. 原因是由于root用户没有SYSTEM_USER权限，把权限加入后即可解决
      2. 需要先给root授权`grant system_user on *.* to 'root';`


## 修改文件存储位置

1. 查看文件存储位置`show  global  variables  like  "%datadir%";`
2. 设置新的存放路径`mkdir -p /data/mysql`
3. 复制原有数据`cp -R /var/lib/mysql/* /data/mysql`
4. 修改权限`chown -R mysql:mysql /data/mysql`
5. 修改配置文件`vim /etc/mysql/my.cnf`
   1. `datadir = /data/mysql`
6. 修改启动文件`vim /etc/apparmor.d/usr.sbin.mysqld`
```bash
#把
/var/lib/mysql r,
/var/lib/mysql/** rwk,
#改成
/data/mysql r,
/data/mysql/** rwk,
```
7. 重启服务和apparmor
```bash
/etc/init.d/apparmor restart
/etc/init.d/mysql restart
```