# vim教程

vim 键盘图：

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g09p6owcisj30sg0k4n4q.jpg)

## 1. vi/vim 的使用

基本上 vi/vim 共分为三种模式，分别是命令模式（Command mode），输入模式（Insert mode）和底线命令模式（Last line mode）。

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g09p8qp8rbj30lv0erwg7.jpg)

## 2. 命令

## 2.1 插入命令

命令|说明
:--:|:--:
i|光标前插入字符
I|在光标所在行行首插入
a|光标后插入字符
A|行尾插入字符
o|光标下方新建一行并进入Insert模式
O|光标上方新建一行并进入Insert模式

## 2.2 定位命令

命令|说明
:--:|:--:
:set nu|显示行号
:set nonu|取消行号
gg|到第一行
G|到最后一行
nG|到第n行
0|行首
$|行尾

## 2.3 删除命令

命令|说明
:--:|:--:
x|删除光标所在字符
X|删除光标左边的字符
nx|删除光标所在后n个字符
dd|删除整行，ndd删除n行
D|删除到行尾

## 2.4 复制和剪切命令

命令|说明
:--:|:--:
yy|复制整行
nyy|复制当前行以下n行
dd|剪切当前行
ndd|剪切当前行以下n行
p|粘贴当前行光标之后
P|粘贴当前行光标之前

## 2.5 替换和取消命令

命令|说明
:--:|:--:
r|替换字符(单个)
R|替换字符(多个)，esc结束
u|撤销操作

## 2.6 退出

命令|说明
:--:|:--:
:w|保存修改
:wq|保存并退出
:q!|丢弃修改并退出 (q退出，有修改的话q不能退出)
:e!|丢弃修改并打开原文件

**参考**：

1. [Vim 利剑常磨，见血封喉](https://mp.weixin.qq.com/s?__biz=MzAxODI5ODMwOA==&mid=2666543500&idx=1&sn=24d3101e953ecaa6400135ca68880229&chksm=80dcfd27b7ab743131b05c971c78a00f824ad7bd61f658670b4be84a672305218210243d08c1&mpshare=1&scene=1&srcid=0106rrvYxxQS4AQBosEwvy88#rd)
2. [Linux vi/vim](http://www.runoob.com/linux/linux-vim.html)