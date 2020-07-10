## VSCODE插件

- Setting Sync: 同步配置，Ctrl + Shift + P后输入sync同步

## IDEA的Tomcat突然不见

- 进去config删除disabled_plugins.txt(或者删除其中某一行相关的),重启IDEA

## IDEA插件

- GrepConsole: console可以设置不同级别log的字体颜色和背景色
- Easy Code: 快速生成数据库实体，dao，mapper
- IdeaVim: vim
- Jrebel: 热部署
- CodeGlance: 在右侧显示代码视图
- Key promoter: 快捷键提醒
- idea-batch
- Alibaba Java Coding Guidelines, p3c: 阿里巴巴出品的java代码规范插件
- FindBugs
- SonarLint: 代码检查
- MyBatisCodeHelperPro: mybatis代码自动生成插件，大部分单表操作的代码可自动生成 减少重复劳动 大幅提升效率
- Free Mybatis plugin: 实现功能，点击dao层方法直接跳转到对应xml
- .ignore: git提交忽略文件
- cmdsupport: cmd文件支持
- AceJump: 快捷跳转
- activate-power-mode: 屏幕颤抖
- Background image Plus: 背景图片
- Background Image Plus +
- Scala: Scala语言
- Material Theme UI: 主题插件
- Rainbow Brackets： 彩色括号
- NyanProgressBar: loading条
- Markdown Support: markdown
- Docker intergration
- GsonFormat: JSON转java类
- Lombok: 省略getter，setter
- Maven helper： maven依赖检查
- VisualVM Launcher: 运行java程序的时候启动visualvm，方便查看jvm的情况 比如堆内存大小的分配
- JVM Debugger Memory View [内存视图按照类名称分组来显示 堆中的对象总数](https://blog.csdn.net/azhegps/article/details/71195351)
- Translation: 翻译
- BashSupport: bash语法支持
- Batch Script Support
- CMD support
- GenerateAllSetter：快速生成一个对象的所有set，默认值为null
- Atom Material Icon
- HighlightBracketPair: 括号高亮
- SequenceDiagram: 时序图
- Solarized Themes: vim主题
- Leetcode Editor
- Jclasslib Bytecode Viewer: 查看字节码
- CamelCase: 大小写
- Julia: 反编译

## 创建快捷方式

- In the Customize IntelliJ IDEA wizard - when you run IntelliJ IDEA for the first time.
- On the Welcome screen: Configure | Create Desktop Entry.
- In the main menu: Tools | Create Desktop Entry.

## linux下搜狗不跟随

通过修改JetBrainsRuntime源码

1. 下载JetBrainsRuntime
   1. github网址: [link](https://github.com/JetBrains/JetBrainsRuntime)
2. 应用patch
   1. [patch](https://github.com/prehonor/myJetBrainsRuntime)
3. 编译JetBrainsRuntime
4. 更改idea 启动sdk
   1. 修改 文件: `home/idea-2020.1/bin/idea.sh` (找到你自己的idea的安装路径)
   2. 在文件开头添加`export IDEA_JDK=/home/prehonor/gitproject/JetBrainsRuntime/build/linux-x86_64-normal-server-release/images/jdk` (改成你自己的编译的jdk所在目录)

## 设置

### leetcode

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
