# 编辑器设置

- [编辑器设置](#编辑器设置)
  - [Sublime Text3插件](#sublime-text3插件)
    - [问题](#问题)
  - [VSCODE插件](#vscode插件)
  - [IDEA](#idea)
    - [IDEA的Tomcat突然不见](#idea的tomcat突然不见)
    - [插件位置](#插件位置)
    - [IDEA插件](#idea插件)
    - [创建快捷方式](#创建快捷方式)
    - [linux下搜狗不跟随](#linux下搜狗不跟随)
    - [设置](#设置)
      - [leetcode](#leetcode)
    - [新版本升级](#新版本升级)
    - [tomcat 控制台乱码](#tomcat-控制台乱码)
    - [命令行快捷键](#命令行快捷键)

## Sublime Text3插件

1. OmniMarkupPreviewer

### 问题

1. OmniMarkupPreviewer 404: 在插件的set-user中写入
```json
{
    "renderer_options-MarkdownRenderer": {
        "extensions": ["markdown.extensions.tables", "markdown.extensions.fenced_code", "markdown.extensions.codehilite"]
    }
}
```


## VSCODE插件

- Setting Sync: 同步配置，Ctrl + Shift + P后输入sync同步

## IDEA

### IDEA的Tomcat突然不见

- 进去config删除disabled_plugins.txt(或者删除其中某一行相关的),重启IDEA\
  
### 插件位置

- 默认位置
  - `C:\Users\tangan\AppData\Roaming\JetBrains\WebStorm2020.1`
- mac
```
~/Library/Preferences/IntelliJIdea2017.2/这里面包含了 fileTemplates templates等配置文件信息
~/Library/Application\ Support/IntelliJIdea2017.2/这里面包含了插件信息
~/Library/Logs/IntelliJIdea.../日志信息
~/Library/Caches/IntelliJIdea... 缓存信息
```

### IDEA插件

- GrepConsole: console可以设置不同级别log的字体颜色和背景色
- Easy Code: 快速生成数据库实体，dao，mapper
- IdeaVim: vim
- Jrebel: 热部署
- CodeGlance: 在右侧显示代码视图
- Key promoter: 快捷键提醒
- idea-batch
- Alibaba Java Coding Guidelines, p3c: 阿里巴巴出品的java代码规范插件
- Alibaba Cloud Toolkit: 快速部署到服务器
- FindBugs
- SonarLint: 代码检查
- MyBatisCodeHelperPro: mybatis代码自动生成插件，大部分单表操作的代码可自动生成 减少重复劳动 大幅提升效率
- Free Mybatis plugin: 实现功能，点击dao层方法直接跳转到对应xml
- .ignore: git提交忽略文件
- cmdsupport: cmd文件支持
- AceJump: 快捷跳转
- activate-power-mode: 屏幕颤抖
- Background Image Plus +
- Scala: Scala语言
- Material Theme UI: 主题插件
- Rainbow Brackets： 彩色括号
- NyanProgressBar: loading条
- Markdown Support: markdown
- Docker intergration
- GsonFormat: JSON转java类
- Json Parser json: json格式化工具，在右侧tool windows bar
- Lombok: 省略getter，setter
- Maven helper： maven依赖检查
- VisualVM Launcher: 运行java程序的时候启动visualvm，方便查看jvm的情况 比如堆内存大小的分配
- JVM Debugger Memory View [内存视图按照类名称分组来显示 堆中的对象总数](https://blog.csdn.net/azhegps/article/details/71195351)
- Translation: 翻译
- BashSupport: bash语法支持
- Batch Script Support
- CMD support
- GenerateAllSetter：快速生成一个对象的所有set，默认值为null
- JUnitGenerator: 自动生成测试代码
- Atom Material Icon
- HighlightBracketPair: 括号高亮
- SequenceDiagram: 时序图
- Solarized Themes: vim主题
- Leetcode Editor
- Jclasslib Bytecode Viewer: 查看字节码
- CamelCase: 大小写
- Julia: 反编译
- RestfulToolkit: rest接口概览，跳转,在方法上右击复制url和参数， java类上添加Convert to JSON功能,在tool window bar,CTR +ALT+N
- Codota:支持智能代码自动提示,搜索相关用法
- MyBatis Log Plugin: 把Mybatis输出的SQL日志还原成完整的SQL语句
- Statistic: 一款代码统计工具，可以用来统计当前项目中代码的行数和大小
- Codota: 代码提示工具，扫描你的代码后，根据你的敲击完美提示

### 创建快捷方式

- In the Customize IntelliJ IDEA wizard - when you run IntelliJ IDEA for the first time.
- On the Welcome screen: Configure | Create Desktop Entry.
- In the main menu: Tools | Create Desktop Entry.

### linux下搜狗不跟随

通过修改JetBrainsRuntime源码

1. 下载JetBrainsRuntime
   1. github网址: [link](https://github.com/JetBrains/JetBrainsRuntime)
2. 应用patch
   1. [patch](https://github.com/prehonor/myJetBrainsRuntime)
3. 编译JetBrainsRuntime
4. 更改idea 启动sdk
   1. 修改 文件: `home/idea-2020.1/bin/idea.sh` (找到你自己的idea的安装路径)
   2. 在文件开头添加`export IDEA_JDK=/home/prehonor/gitproject/JetBrainsRuntime/build/linux-x86_64-normal-server-release/images/jdk` (改成你自己的编译的jdk所在目录)

### 设置

#### leetcode

1. CodeFileName: 设置为显示题目的英文名称
> $!velocityTool.camelCaseName(${question.titleSlug})  
2. CodeTemplate: 
```java
${question.content}
package test.leetcode.editor.cn;
public class $!velocityTool.camelCaseName(${question.titleSlug}) {
    public static void main(String[] args) {
        Solution solution = new $!velocityTool.camelCaseName(${question.titleSlug})().new Solution();
    }
    ${question.code}
}
```

### 新版本升级

1. 修改配置文件: `idea/bin/idea.properties`
2. 破解: `idea/bin/idea64.vmoptions`
3. 修改jdk: `idea/bin/idea.sh`
4. 备注
   1. vim打开多个文件
   2. 使用命令`:split`水平分割，`:vsplit`垂直分割
   3. 使用`ctrl+w`然后按`hjkl`跳转方向
   4. `:close`关闭当前窗口，`:only`关闭其他所有窗口
   5. `ctrl+6`下一个文件，`:bn`下一个文件，`bp`上一个文件

### tomcat 控制台乱码
1. 找到tomcat 安装目录下的 conf /logging.properties 文件打开
2. 将 java.util.logging.ConsoleHandler.encoding = UTF-8修改为java.util.logging.ConsoleHandler.encoding = GBK
3. 保存后 重启idea


### 命令行快捷键

1. CTRL + U - 剪切光标前的内容
2. CTRL + K - 剪切光标至行末的内容
3. CTRL + Y - 粘贴
4. CTRL + E - 移动光标到行末
5. CTRL + A - 移动光标到行首
6. ALT + F - 跳向下一个空格
7. ALT + B - 跳回上一个空格
8. ALT + Backspace - 删除前一个单词
9. CTRL + W - 剪切光标前一个单词
10. Shift + Insert - 向终端内粘贴文本 (CTRL+SHIFT+V)