#### maven项目不能自动导入
- 项目根目录命令行输入：
    + mvn -U idea:idea
    + mvn idea:module 生成.iml文件
    + mvn idea:project 生成.ipr文件
    + mvn idea:workspace 生成.iws文件