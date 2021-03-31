# 网络工具

- [网络工具](#网络工具)
  - [Clumsy](#clumsy)
    - [Filter语法](#filter语法)


## [Clumsy](https://jagt.github.io/clumsy/download.html)

[clumsy](https://github.com/jagt/clumsy) 能在 Windows 平台下人工造成不稳定的网络状况，方便你调试应用程序在极端网络状况下的表现  

clumsy 首先根据用户选择的 filter 来拦截指定的网络数据。在 filter 中可以设定你感兴趣的协议(tcp/udp)，端口号，是接收还是发出的端口。你也可以通过简单的逻辑语句来进一步缩小范围。当 clumsy 被激活时，只有符合这些标准的网络数据会被进行处理，而你不感兴趣的数据仍然会由系统正常传输。

当被 filter 的网络数据包被拦截后，你可以选择 clumsy 提供的功能来有目的性的调整网络情况：

1. 延迟(Lag)，把数据包缓存一段时间后再发出，这样能够模拟网络延迟的状况。
2. 丢包(Drop)，随机丢弃一些数据。
3. 节流(Throttle)，把一小段时间内的数据拦截下来后再在之后的同一时间一同发出去。
4. 重发(Duplicate)，随机复制一些数据并与其本身一同发送。
5. 乱序(Out of order)，打乱数据包发送的顺序。
6. 篡改(Tamper)，随机修改小部分的包裹内容

![](pic/clumsy_0.png)

### Filter语法

| outbound | 是否为输出数据包 |
| -------- | ---------------- |
| inbound  | 是否为输入数据包 |
