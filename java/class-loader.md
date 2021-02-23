# 类加载器


## 概述

1. bootstrap classloader: 负责加载java的核心类（jre下lib和class目录的内容）
2. extension classloader: 负责加载java的扩展类（jre下的lib/ext目录的内容）
3. system classloader: 负责加载应用指定的类（环境变量classpath中配置的内容）


## getResourceAsStream

1. class的getResourceAsStream
   1. class.getResourceAsStream(“path”): path不以’/‘开头时，默认是指所在类的相对路径，从这个相对路径下取资源
   2. class.getResourceAsStream("/path"): path以’/'开头时，则是从项目的ClassPath根下获取资源，就是要写相对于classpath类路径下的绝对路径
2. classloader的getResourceAsStream
   1. path不能以`/`开头，path是指类加载器的加载范围，在资源加载的过程中，使用的逐级向上委托的形式加载的，`/`表示Boot ClassLoader，类加载器中的加载范围，因为这个类加载器是C++实现的，所以加载范围为null
3. class.getResourceAsStream最终调用是ClassLoader.getResourceAsStream，这两个方法最终调用还是ClassLoader.getResource()方法加载资源