# 问题

## Thread中start()与run()

线程一般有五种状态：创建，就绪，运行，阻塞，死亡  
创建：生成对象后，start之前  
就绪：start后，线程调度正式运行前  
运行：执行run中的代码  
阻塞：正在运行时被暂停或等待某个时机，sleep，wait,suspend  
死亡：执行完run后，或调用stop后，无法再用start进入就绪  

1. start启动的是异步多线程
2. run只是个普通同步方法
