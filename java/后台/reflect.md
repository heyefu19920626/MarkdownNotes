# 反射

## 获取类的三种方式

1. ``Class.forName(String classPath)``
   1. classPath是类的完整路径，可能产生ClassNotFoundException异常
   2. 一般用于不知道类是否存在的情况
2. ``Class clz = 类名.class``
   1. 一般用于知道有这个类的情况
3. ``Class clz = 对象.getClass``
   1. 前提是对象已经实例化了
   2. 可以用于动态调用方法


## 调用方法

1. 获取具体的方法``Method method = clz.getMethod(name, parameterTypes...)``
   1. name为方法名，parameterTypes为参数类型
   2. 调用``method.invoke(object, args...)``
   3. object为实例化的对象,args为该方法所需要的参数