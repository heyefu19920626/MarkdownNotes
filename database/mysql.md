### 目录

- [日期更新](#date-update)

<div id="date-update"></div>

#### Mysql中创建日期和更新日期自动更新

- 创建时间字段
> `creat_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP  

- 更新时间字段
>  `update_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP

- 在navcat中
    + 设置默认值为CURRENT_TIMESTAMP
    + 勾选not null
- 当Mysql字段类型设置为timestamp，且默认值设置为CURRENT_TIMESTAMP，并不为空时，如果插入时设置该字段为NULL则报错
> 修改mysql的配置文件my.ini中将explicit_defaults_for_timestamp=true注释或者改为fales

- [CURRENT_TIMESTAMP](#time)



<div id="time"></div>

#### CURRENT_TIMESTAMP
