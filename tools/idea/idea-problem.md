# 问题

- [问题](#问题)
  - [Linux创建桌面图标](#linux创建桌面图标)
  - [maven项目不能自动导入](#maven项目不能自动导入)
  - [Idea多次启动同一个项目](#idea多次启动同一个项目)

## Linux创建桌面图标

官方提供三种方式

1. In the Customize IntelliJ IDEA wizard - when you run IntelliJ IDEA for the first time.
2. On the Welcome screen: Configure | Create Desktop Entry.
3. In the main menu: Tools | Create Desktop Entry.

## maven项目不能自动导入

- 项目根目录命令行输入：
    1. mvn -U idea:idea
    2. mvn idea:module 生成.iml文件
    3. mvn idea:project 生成.ipr文件
    4. mvn idea:workspace 生成.iws文件

## Idea多次启动同一个项目

1. 进入配置页Run -> Edit Configurations
2. 勾选最右边的Allow parallel run

