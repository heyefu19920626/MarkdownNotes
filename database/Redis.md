# Redis

- [..](database-catalog.md)

- [Redis](#redis)
  - [登录密码](#登录密码)
  - [非主机登录](#非主机登录)
  - [启动多个实例](#启动多个实例)
  - [基础](#基础)
  - [非本机不能连接Redis](#非本机不能连接redis)
  - [redis的key过期监听](#redis的key过期监听)
    - [单机监听](#单机监听)
    - [集群监听](#集群监听)
  - [分布式锁](#分布式锁)
  - [RedLock算法加锁](#redlock算法加锁)

## 登录密码

1. 修改/etc/redis/redis.conf
   1. 取消requirpass,并设置值
2. 命令行登录
   1. redis-cli -h $host -p $port -a $password
   2. redis-cli -h $host -p $port进入后再使用auth $password认证

## 非主机登录

1. 修改/etc/redis/redis.conf
   1. 取消bind 127.0.0.1，并设置密码
   2. 取消bind 127.0.0.1，关闭protected-mode(将值设为no)

## 启动多个实例

1. redis-server --port 6380 &
2. redis-server /etc/redis/redis_6380.conf
```bash
#vi redis6380.conf
pidfile : pidfile/var/run/redis/redis_6380.pid
port 6380
logfile : logfile/var/log/redis/redis_6380.log
rdbfile : dbfilenamedump_6380.rdb
```

## 基础

磁盘： 寻址，带宽  
磁盘毫秒级，内存纳秒级  
数据库，文件，分而治之,读写分离  
写会牵扯到索引的变化，会变慢;查询通过内存中的B+Tree,快速命中索引;  
redis,memcache内存型数据库  
redis的value有类型,基于类型有对应的方法,减少了IO量  
redis业务处理的时候是单线程的
BIO(Block):一个线程阻塞，新出线程处理新的客户端  
NIO(Unblock):用户态，内核态  
select多路复用  
epoll多路复用: epoll_create,epoll_ctl,epoll_wait
``strace -ff -o ~/starcedir/ooxx ./redis-server``  
kafaka: MMAP(应用内核共享空间), 零拷贝(sendfile)  
redis6.x: io thread 出发read读取数据  
redis的value五大类型:string, list, hash map, set, sorted set;  
二进制安全，字节数组;redis存的是字节;bit map,位图;  
让程序没有状态，状态存入redis，微服务的无状态化

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

## redis的key过期监听

1. 修改redis.conf中的notify-keyspace-events "Ex",原本是修改redis.conf中的notify-keyspace-events ""
2. 过期时只能拿到key

### 单机监听

1. 配置信息
```yml
spring:
  redis:
    host: 192.168.241.239
    password: 123456
    port: 6379
```
2. 配置环境类
```java
package com.young.springdemo.config.redis;
 
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
 
@Configuration
public class RedisListenerConfig {
    @Bean
    RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory) {
        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        return container;
    }
}
```
3. 监听器
```java
package com.young.springdemo.config.redis;
 
import lombok.extern.slf4j.Slf4j;
import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.listener.KeyExpirationEventMessageListener;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
import org.springframework.stereotype.Component;
 
@Component
@Slf4j
public class RedisKeyExpirationListener extends KeyExpirationEventMessageListener {
    public RedisKeyExpirationListener(RedisMessageListenerContainer listenerContainer) {
        super(listenerContainer);
    }
 
    @Override
    public void onMessage(Message message, byte[] pattern) {
        // 用户做自己的业务处理即可,注意message.toString()可以获取失效的key
        String expiredKey = message.toString();
        log.info("key[{}] 已过期", expiredKey);
        //todo 业务
    }
}
```
### 集群监听

1. 配置集群信息
```yml
spring:
  redis:
    password: 123456
    cluster:
      nodes: 192.168.241.239:7000,192.168.241.239:7001,192.168.241.239:7002,192.168.241.239:7003,192.168.241.239:7004,192.168.241.239:7005
```
2. 初始化配置
```java
package com.young.springdemo.config.redis;
 
import org.springframework.beans.factory.BeanFactory;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnExpression;
import org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.jedis.JedisConnection;
import org.springframework.data.redis.core.RedisOperations;
import org.springframework.data.redis.listener.PatternTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
 
import redis.clients.jedis.Jedis;
 
@Configuration
@ConditionalOnClass({ JedisConnection.class, RedisOperations.class, Jedis.class, MessageListener.class })
@AutoConfigureAfter({ JacksonAutoConfiguration.class,RedisAutoConfiguration.class })
public class RedisAutoConfiguration {
    @Configuration
    @ConditionalOnExpression("!'${spring.redis.host:}'.isEmpty()")
    public static class RedisStandAloneAutoConfiguration {
        @Bean
        public RedisMessageListenerContainer customizeRedisListenerContainer(
                RedisConnectionFactory redisConnectionFactory,MessageListener messageListener) {
            RedisMessageListenerContainer redisMessageListenerContainer = new RedisMessageListenerContainer();
            redisMessageListenerContainer.setConnectionFactory(redisConnectionFactory);
            redisMessageListenerContainer.addMessageListener(messageListener,new PatternTopic("__keyevent@0__:expired"));
            return redisMessageListenerContainer;
        }
    }
 
 
    @Configuration
    @ConditionalOnExpression("'${spring.redis.host:}'.isEmpty()")
    public static class RedisClusterAutoConfiguration {
        @Bean
        public RedisMessageListenerFactory redisMessageListenerFactory(BeanFactory beanFactory,
                                                                       RedisConnectionFactory redisConnectionFactory) {
            RedisMessageListenerFactory beans = new RedisMessageListenerFactory();
            beans.setBeanFactory(beanFactory);
            beans.setRedisConnectionFactory(redisConnectionFactory);
            return beans;
        }
    }
}
```
3. 监听配置类
```java
package com.young.springdemo.config.redis;
 
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.BeanFactoryAware;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.support.DefaultListableBeanFactory;
import org.springframework.context.ApplicationListener;
import org.springframework.context.event.ContextRefreshedEvent;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.data.redis.connection.RedisClusterConnection;
import org.springframework.data.redis.connection.RedisClusterNode;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.listener.PatternTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
 
import redis.clients.jedis.JedisShardInfo;
 
@Slf4j
public class RedisMessageListenerFactory implements BeanFactoryAware, ApplicationListener<ContextRefreshedEvent> {
 
    @Value("${spring.redis.password}")
    private String password;
 
    private DefaultListableBeanFactory beanFactory;
 
    private RedisConnectionFactory redisConnectionFactory;
 
    @Autowired
    private MessageListener messageListener;
 
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        this.beanFactory = (DefaultListableBeanFactory) beanFactory;
    }
 
    public void setRedisConnectionFactory(RedisConnectionFactory redisConnectionFactory) {
        this.redisConnectionFactory = redisConnectionFactory;
    }
 
    @Override
    public void onApplicationEvent(ContextRefreshedEvent contextRefreshedEvent) {
        RedisClusterConnection redisClusterConnection = redisConnectionFactory.getClusterConnection();
        if (redisClusterConnection != null) {
            Iterable<RedisClusterNode> nodes = redisClusterConnection.clusterGetNodes();
            for (RedisClusterNode node : nodes) {
                if (node.isMaster()) {
                    log.info("获取到redis的master节点为[{}]",node.toString());
                    String containerBeanName = "messageContainer" + node.hashCode();
                    if (beanFactory.containsBean(containerBeanName)) {
                        return;
                    }
                    JedisShardInfo jedisShardInfo = new JedisShardInfo(node.getHost(), node.getPort());
                    jedisShardInfo.setPassword(password);
                    JedisConnectionFactory factory = new JedisConnectionFactory(jedisShardInfo);
                    BeanDefinitionBuilder containerBeanDefinitionBuilder = BeanDefinitionBuilder
                            .genericBeanDefinition(RedisMessageListenerContainer.class);
                    containerBeanDefinitionBuilder.addPropertyValue("connectionFactory", factory);
                    containerBeanDefinitionBuilder.setScope(BeanDefinition.SCOPE_SINGLETON);
                    containerBeanDefinitionBuilder.setLazyInit(false);
                    beanFactory.registerBeanDefinition(containerBeanName,
                            containerBeanDefinitionBuilder.getRawBeanDefinition());
 
                    RedisMessageListenerContainer container = beanFactory.getBean(containerBeanName,
                            RedisMessageListenerContainer.class);
                    String listenerBeanName = "messageListener" + node.hashCode();
                    if (beanFactory.containsBean(listenerBeanName)) {
                        return;
                    }
                    container.addMessageListener(messageListener, new PatternTopic("__keyevent@0__:expired"));
                    container.start();
                }
            }
        }
    }
 
}
```
4. 具体的监听器
```java
package com.young.springdemo.config.redis;
 
import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.stereotype.Component;
@Component
public class KeyExpiredEventMessageListener implements MessageListener {
 
    @Override
    public void onMessage(Message message, byte[] pattern) {
        String expired = message.toString();
        System.out.println("======接收监听===="+expired);
    }
 
}
```

## [分布式锁](https://www.cnblogs.com/fixzd/p/9479970.html)

分布式锁需要具备：  
1. 互斥性：在任意一个时刻，只有一个客户端持有锁
2. 无死锁：即便持有锁的客户端崩溃或者其他意外事件，锁仍然可以被获取
3. 容错：只要大部分Redis节点都活着，客户端就可以获取和释放锁

版本1：
加锁 -> 释放锁
版本2：
加锁，设置过期时间 -> 释放锁
版本3：
加锁，设置过期时间，设置锁的value -> 释放锁
版本4：
加锁，设置过期时间，设置锁的value -> 具有原子性的释放锁
版本5：
确保过期时间大于业务执行时间
加锁，设置过期时间，设置锁的value,设置线程不断刷新过期时间 -> 具有原子性的释放锁

## RedLock算法加锁

1. 获取当前Unix时间，以毫秒为单位。
2. 依次尝试从N个实例，使用相同的key和随机值获取锁。在步骤2，当向Redis设置锁时,客户端应该设置一个网络连接和响应超时时间，这个超时时间应该小于锁的失效时间。例如你的锁自动失效时间为10秒，则超时时间应该在5-50毫秒之间。这样可以避免服务器端Redis已经挂掉的情况下，客户端还在死死地等待响应结果。如果服务器端没有在规定时间内响应，客户端应该尽快尝试另外一个Redis实例
3. 客户端使用当前时间减去开始获取锁时间（步骤1记录的时间）就得到获取锁使用的时间。当且仅当从大多数（这里是3个节点）的Redis节点都取到锁，并且使用的时间小于锁失效时间时，锁才算获取成功
4. 如果取到了锁，key的真正有效时间等于有效时间减去获取锁所使用的时间（步骤3计算的结果）
5. 如果因为某些原因，获取锁失败（没有在至少N/2+1个Redis实例取到锁或者取锁时间已经超过了有效时间），客户端应该在所有的Redis实例上进行解锁（即便某些Redis实例根本就没有加锁成功）