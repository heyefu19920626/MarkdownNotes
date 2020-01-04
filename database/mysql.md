# Mysql

- [..](database-catalog.md)


- [Mysql](#mysql)
  - [Mariadb修改密码](#mariadb修改密码)
    - [知道密码](#知道密码)
    - [忘记密码](#忘记密码)
  - [Mariadb允许远程访问](#mariadb允许远程访问)
  - [导入与导出数据库文件](#导入与导出数据库文件)
    - [导出](#导出)
    - [导入](#导入)
  - [Mysql中创建日期和更新日期自动更新](#mysql中创建日期和更新日期自动更新)

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

