# VIM

- [VIM](#vim)
  - [翻屏](#翻屏)
  - [标记](#标记)
  - [插入](#插入)
  - [文本对象](#文本对象)
  - [寄存器](#寄存器)
  - [字符串替换](#字符串替换)
  - [重复上次命令](#重复上次命令)
  - [宏](#宏)


## 翻屏

1. ctrl+f: 下翻一屏。
2. ctrl+b: 上翻一屏。
3. ctrl+d: 下翻半屏。
4. ctrl+u: 上翻半屏。
5. ctrl+e: 向下滚动一行。
6. ctrl+y: 向上滚动一行。
7. n%: 到文件n%的位置。
8. zz: 将当前行移动到屏幕中央。
9. zt: 将当前行移动到屏幕顶端。
10. zb: 将当前行移动到屏幕底端。

## 标记

使用标记可以快速移动。到达标记后，可以用Ctrl+o返回原来的位置

1. m{a-z}: 标记光标所在位置，局部标记，只用于当前文件。
2. m{A-Z}: 标记光标所在位置，全局标记。标记之后，退出Vim， 重新启动，标记仍然有效。
3. `{a-z}: 移动到标记位置。
4. '{a-z}: 移动到标记行的行首。
5. `{0-9}：回到上[2-10]次关闭vim时最后离开的位置。
6. \`\`: 移动到上次编辑的位置。''也可以，不过``精确到列，而''精确到行 。如果想跳转到更老的位置，可以按C-o，跳转到更新的位置用C-i。
7. `": 移动到上次离开的地方。
8. `.: 移动到最后改动的地方。
9. :marks 显示所有标记。
10. :delmarks a b -- 删除标记a和b。
11. :delmarks a-c -- 删除标记a、b和c。
12. :delmarks a c-f -- 删除标记a、c、d、e、f。
13. :delmarks! -- 删除当前缓冲区的所有标记。
14. :help mark-motions 查看更多关于mark的知识

## 插入

:r filename在当前位置插入另一个文件的内容。
:[n]r filename在第n行插入另一个文件的内容。
:r !date 在光标处插入当前日期与时间。同理，:r !command可以将其它shell命令的输出插入当前文档

## 文本对象

1. aw：一个词
2. as：一句。
3. ap：一段。
4. ab：一块（包含在圆括号中的）

## 寄存器

1. a-z：都可以用作寄存器名。"ayy把当前行的内容放入a寄存器。
2. A-Z：用大写字母索引寄存器，可以在寄存器中追加内容。 如"Ayy把当前行的内容追加到a寄存器中。
3. :reg 显示所有寄存器的内容。
4. ""：不加寄存器索引时，默认使用的寄存器。
5. "*：当前选择缓冲区，"*yy把当前行的内容放入当前选择缓冲区。
6. "+：系统剪贴板。"+yy把当前行的内容放入系统剪贴板。

## 字符串替换

1. :s/vivian/sky/ 替换当前行第一个 vivian 为 sky
2. :s/vivian/sky/g 替换当前行所有 vivian 为 sky
3. :n,$s/vivian/sky/ 替换第 n 行开始到最后一行中每一行的第一个 vivian 为 sky
4. :n,$s/vivian/sky/g 替换第 n 行开始到最后一行中每一行所有 vivian 为 sky
5. n 为数字，若 n 为 .，表示从当前行开始到最后一行
6. :%s/vivian/sky/(等同于 :g/vivian/s//sky/) 替换每一行的第一个 vivian 为 sky
7. :%s/vivian/sky/g(等同于 :g/vivian/s//sky/g) 替换每一行中所有 vivian 为 sky
8. 可以使用 # 作为分隔符，此时中间出现的 / 不会作为分隔符
9. :s#vivian/#sky/# 替换当前行第一个 vivian/ 为 sky/
10. :%s+/oradata/apras/+/user01/apras1+ (使用+ 来 替换 / )： /oradata/apras/替换成/user01/apras1/

## 重复上次命令

1. 按`.`键

## 宏

重复上一个编辑动作  
1. qa: 开始录制宏a(键盘操作记录)
2. q: 停止录制
3. @a: 播放宏a
4. 10@a: 播放10次宏a
5. Ctrl + A 数字+1
6. Ctrl + X 数字-1