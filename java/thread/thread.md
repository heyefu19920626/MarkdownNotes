# 多线程

[..](../java-catalog.md)

- [多线程](#多线程)
  - [ExecutorService与Executor接口](#executorservice与executor接口)
  - [Callable和Runnable接口](#callable和runnable接口)
  - [Future接口](#future接口)
  - [FutureTask类](#futuretask类)
  - [ThreadPoolExecutor线程池](#threadpoolexecutor线程池)


## ExecutorService与Executor接口

1. Executor只有execute执行方法，接收一个Runable，没有返回值
2. ExecutorService继承Execute接口,有submit方法，可以接收Runable和Callcable，返回Future
3. submit可以在主线程中捕获子线程的异常



## Callable和Runnable接口

```java
public interface Callable<V> {
　　V call() throws Exception;
}
interface Runnable {
　　public abstract void run();
}
```
1. Callable能接受一个泛型，然后在call方法中返回一个这个类型的值。而Runnable的run方法没有返回值
2. Callable的call方法可以抛出异常，而Runnable的run方法不会抛出异常

## Future接口

1. 可以通过Future获得任务执行的返回值
2. Future的get方法会阻塞主线程，等待任务的执行结果
3. Runable虽然没有返回值,但可以在submit的时候添加一个result参数，并在实例化Runable的时候将该result传递给Runable

## FutureTask类

1. FutureTask即实现了Runable又实现了Callable
2. FutureTask可以直接使用Executor执行
3. FutureTask新建的时候实际上吧Runable转化为了Callable

## ThreadPoolExecutor线程池

1. ThreadPoolExecutor实现了ExecutorService接口
2. 实例化ThreadPoolExecutor需要传递参数
   1. corePoolSize： 线程池核心线程数
   2. maximumPoolSize：线程池最大数
   3. keepAliveTime： 空闲线程存活时间
   4. unit： 时间单位
   5. workQueue： 线程池所使用的缓冲队列
   6. threadFactory：线程池创建线程使用的工厂
   7. handler： 线程池对拒绝任务的处理策略, RejectedExecutionHandler来处理；ThreadPoolExecutor内部有实现4个拒绝策略，默认为AbortPolicy策略：
      1. CallerRunsPolicy：由调用execute方法提交任务的线程来执行这个任务
      2. AbortPolicy：抛出异常RejectedExecutionException拒绝提交任务
      3. DiscardPolicy：直接抛弃任务，不做任何处理
      4. DiscardOldestPolicy：去除任务队列中的第一个任务，重新提交
3. 当池中正在运行的线程数（包括空闲线程数）小于corePoolSize时，新建线程
4. 当池中正在运行的线程数（包括空闲线程数）大于等于corePoolSize时，新插入的任务进入workQueue排队(如果workQueue长度允许)，等待空闲线程来执行
5. **当队列里的任务达到上限**，并且池中正在进行的线程小于maxinumPoolSize,**对于新加入的任务**，新建线程
6. 队列里的任务达到上限，并且池中正在运行的线程等于maximumPoolSize，对于新加入的任务，执行拒绝策略（线程池默认的策略是抛异常）
7. 关闭线程
   1. pool.shutdown();//平缓关闭，不允许新的线程加入，正在运行的都跑完即可关闭
   2. pool.shutdownNow();//暴力关闭。不允许新的线程加入，且直接停到正在进行的线程