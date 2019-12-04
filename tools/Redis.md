# Redis

## 非本机不能连接Redis

通过修改redis.conf文件解决，一般在/etc/redis.conf，也可以通过``find / -name /etc/redis.conf``寻找

1. 是否设置了密码
   1. 在redis.conf中设置``requirepass password``
   2. ./redis-cli启动redis客户端
   3. ``auth "password"`` password换成上面设置的密码,如果出现OK则说明设置成功
2. 是否允许非主机连接
   1. 注释``bind 127.0.0.1``
   2. 添加``bind 0.0.0.0``
3. 防火墙是否开启6379端口
   1. ``firewall-cmd --query-port=6379/tcp``如果出现no，执行``firewall-cmd --add-port=6379/tcp``返回success