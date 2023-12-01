# Java 目录

- [..](../README.md)
- [Spring](./Spring/spring-catalog.md)
- [java](./java.md)
- [Java Problem](Java-Problem.md)
- [thread](thread/thread.md)


## jdk输出jre

使用命令在当前目录输出jre`./jdk-17/bin/jlink --module-path jdk-17/jmods --add-modules java.desktop --output jre`

从 JDK 9 开始，Oracle JDK 和 OpenJDK 开始使用模块化系统，不再提供单独的 JRE（Java Runtime Environment）安装包。相反，您可以使用 JDK 包，其中包含了 JRE 所需的所有组件。

可以使用`--add-modules ALL-MODULE-PATH`包含所有模块