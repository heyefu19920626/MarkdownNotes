# Mysql

- [..](database-catalog.md)

- [日期更新CURRENT_TIMESTAMP](#date-update)


## 导入与导出数据库文件

### 导出
1. 导出数据库与文件
   1. mysqldump -u 用户名 -p 数据库名 > 文件名.sql:`mysqldump -u root -p boot_template > boot.sql`
2. 只导出结构
   1. mysqldump -u 用户名 -p -d 数据库名 > 文件名.sql:`mysqldump -u root -p -d boot_template > boot_stru.sql`

### 导入
1. 在mysql控制台导入
   1. 首先建空数据库`mysql>create database abc;`
   2. 选择数据库`use abc;`
   3. 设置数据库编码`set names utf8;`
   4. 导入数据（注意sql文件的路径）`source /home/heyefu/Downloads/abc.sql;`
2. 在终端导入
   1. mysql -u 用户名 -p 数据库名 < 数据库名.sql:`mysql -u root -p abc < abc.sql`
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

