# linux 下 navicat 安装

**纯破解版参考（不会过期）**：https://www.cnblogs.com/kaerxifa/p/12048976.html

**下载**：[https://www.navicat.com.cn/](https://www.navicat.com.cn/)

- 最新 navicat 15 是 .appimage 格式，可以直接双击运行（右键属性赋予可执行权限）

- 打开终端，输入如下命令：

  sudo vim /usr/share/applications/navicat.desktop，其中 navicat.desktop 是即将创建的文件图标

- 将下面的内容粘贴到 navicat.desktop 文件中：

  ```shell
      [Desktop Entry]
      Name=navicat
      Exec=/home/qiubing/qiubing/navicat/navicat15-premium-cs.AppImage
      Comment=navicat
      Icon=/home/qiubing/qiubing/navicat/navicat.png
      Type=Application
      Terminal=false
      Encoding=UTF-8
  ```

  ==注意将 /home/qiubing/qiubing/navicat/ 这段路径换成自己的情况。==

  其中 `Exec` 就是平时用终端打开 `IntelliJ IDEA` 的命令， `Icon` 是图标文件。

- 点击 `win` 键即可找到对应图标，右键添加至收藏夹

**参考**：[Ubuntu下为AppImage应用添加图标并添加到应用](https://zhuanlan.zhihu.com/p/215507075)

### 可能出现的错误

1. ubuntu 18.04 使用 navicat 连接本地 mysql 提示权限不够

   ![image-20201016192516494](https://i.loli.net/2020/10/16/Ef7pKOGX9CVQTqJ.png)

   把连接配置中主机里的*localhost改成127.0.0.1*就可以解决

   **参考**：[尝试使用navicat](https://blog.csdn.net/qq_42848693/article/details/108604739)

---

## navicat 过期解决办法

==先参考图片中的方法,不一定能用,如果用不了再用下面的方法==

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g76utjx7jfj30k80gg76d.jpg)

进入 `~` 目录,删除 `.navicat64`.

`rm -rf ~/.navicat64/`

接下来重新启动navicat就可以了，它会自动重新安装一遍，并且试用期重新计时。

**参考**:

- [ubuntu系统下navicat 试用到期解决方案](https://www.cnblogs.com/lpdeboke/p/11263120.html)

- [Ubuntu下navicat过期解决办法](https://www.cnblogs.com/shaojiafeng/p/10728603.html)

---
