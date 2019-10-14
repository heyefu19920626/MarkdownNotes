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