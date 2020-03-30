# 分布式

[..](../README.md)

[zooker](zooker.md)

- [分布式](#分布式)
  - [分布式ID](#分布式id)


## 分布式ID

1. UUID
2. 数据库自增ID
3. 数据库多主模式, 不同的起始值，不同的步长
4. 号段模式, 批量获取号段，本地在号段内新增
5. Redis, incr命令来实现原子性的自增与返回
6. 雪花算法, 时间戳+机器id+序列号
7. 滴滴Tinyid
8. 百度Uidgenerator
9. 美团Leaf