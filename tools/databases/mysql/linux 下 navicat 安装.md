# linux 下 navicat 安装

**下载**：[https://www.navicat.com.cn/](https://www.navicat.com.cn/)

下载完成，解压后进入解压后的目录运行 `./start_navicat`，一路默认即可。

## 1. 解决中文乱码问题

1. 将 `.start_navicat` 中 编码方式修改为 `export LANG="zh_CN.utf8"`

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g6c9dzt28kj30jf0bztat.jpg)

2. 在连接数据库时，高级选项中，设置编码为UTF-8

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g6ca55iyiej30is0kggml.jpg)

3. 选择工具-首选项，常规页签下，字体选择Noto Sans Mono CJK TC Regular

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g6ca67cin4j312e0odmzo.jpg)

4. 编辑器页签下编辑器字体选择Noto Sans CJK SC Regular

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g6ca6lrxqgj30qg0lqq4h.jpg)

5. 记录页签下网格字体选择Noto Sans Mono CJK TC Regular

    ![2](http://ww1.sinaimg.cn/large/006alGmrgy1g6ca710iipj30qv0juwfh.jpg)

修改完成后，重启软件后，编码问题即可解决！

快捷方式，参考 linux/ubuntu/ 下教程

**参考**：

- [ubuntu 16.04安装navicat](https://blog.csdn.net/qq_25543685/article/details/72638399)

- [Ubuntu下Navicat乱码问题终极解决](https://blog.csdn.net/yuxiao97/article/details/84886921)

---

## 2. navicat 过期解决办法

==先参考图片中的方法,不一定能用,如果用不了再用下面的方法==

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g76utjx7jfj30k80gg76d.jpg)

进入 `~` 目录,删除 `.navicat64`.

`rm -rf ~/.navicat64/`

接下来重新启动navicat就可以了，它会自动重新安装一遍，并且试用期重新计时。

**参考**:

- [ubuntu系统下navicat 试用到期解决方案](https://www.cnblogs.com/lpdeboke/p/11263120.html)

- [Ubuntu下navicat过期解决办法](https://www.cnblogs.com/shaojiafeng/p/10728603.html)

---
