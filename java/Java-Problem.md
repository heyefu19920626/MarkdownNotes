# 问题

- [问题](#问题)
  - [Thread中start()与run()](#thread中start与run)
  - [三种代理模式](#三种代理模式)
  - [Java对象不再使用时，赋值为null](#java对象不再使用时赋值为null)

## Thread中start()与run()

线程一般有五种状态：创建，就绪，运行，阻塞，死亡  
创建：生成对象后，start之前  
就绪：start后，线程调度正式运行前  
运行：执行run中的代码  
阻塞：正在运行时被暂停或等待某个时机，sleep，wait,suspend  
死亡：执行完run后，或调用stop后，无法再用start进入就绪  

1. start启动的是异步多线程
2. run只是个普通同步方法

## 三种代理模式

1. 静态代理  
   代理对象要实现与目标对象一样的接口
2. 动态代理,JDK代理,接口代理  
   使用java.lang.reflect.Proxy.newProxyInstance的方法  
   代理对象不需要实现接口，目标对象需要实现接口
```java
static Object newProxyInstance(ClassLoader loader, 
            Class[] interfaces,InvocationHandler h )
```
3. Cglib代理,子类代理

## Java对象不再使用时，赋值为null

如何确定对象可以被回收/如何确定对象是存活的？  
可达性分析算法  
JVM: 栈中引用的对象。只要堆中的对象，在栈中还存在引用，就是存活的。  
赋值操作会对栈中进行读写，会擦掉已失效的引用。
```java
if(true){
    byte[] placeHolder = new byte[64 * 1024 * 1024]
    System.out.println(placeHolder.length / 1024);
}
//或者换位其它的赋值操作,如int replace = 1;也能达到同样效果
placeHolder = null;
System.gc()
```