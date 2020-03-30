# zooker

[..](distributed-catalog.md)


- [zooker](#zooker)
  - [脑裂问题](#脑裂问题)



## 脑裂问题


脑裂(split-brain)就是“大脑分裂”，也就是本来一个“大脑”被拆分了两个或多个“大脑”.脑裂通常会出现在集群环境中，比如ElasticSearch、Zookeeper集群，而这些集群环境有一个统一的特点，就是它们有一个大脑，比如ElasticSearch集群中有Master节点，Zookeeper集群中有Leader节点.  

1. 过半机制:在领导者选举的过程中，如果某台zkServer获得了超过半数的选票，则此zkServer就可以成为Leader
   1. 大于半数而不是大于等于是为了防止脑裂问题