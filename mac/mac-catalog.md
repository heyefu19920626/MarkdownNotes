# Mac

- [Mac](#mac)
  - [允许安装任何来源](#允许安装任何来源)
  - [键盘映射](#键盘映射)
  - [快捷键](#快捷键)
  - [包管理器Homebrew](#包管理器homebrew)
    - [7z](#7z)


## 允许安装任何来源

1. 命令行输入`sudo spctl --master-disable`


## 键盘映射

1、Home键=Fn+左方向
2、End键=Fn+右方向
3、PageUP=Fn+上方向、
4、PageDOWN=Fn+下方向、
5、向前Delete=Fn+delete键



## 快捷键

1. 应用最小化：Command + M
2. 从最下画恢复： Command + tab切换到，松开tab，按住option,松开command
3. 固定大写：control + caps
4. 强制关闭程序：command+option+esc
5. 清空回收站：command+shift+delete
6. 移到回收站: command+delete



## 包管理器Homebrew

[官网](https://brew.sh/index.html)

1. 安装Homebrew: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"`
2. 安装其他工具`brew install wget`
3. 卸载其他工具`brew uninstall wget`
4. 列出已安装的软件`brew list`
5. brew update 更新
6. brew brew home 用浏览器打开brew的官方网站
7. brew info 显示软件信息
8. brew deps 显示包依赖

### 7z

1. 安装： `brew install pzip`
2. 压缩 sputnik 文件夹下的所有文件`7z a heed.7z sputnik`
3. 解压 heed.7z`7z x heed.7z`