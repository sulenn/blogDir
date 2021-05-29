# appimage快捷方式创建

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

- 点击 `win` 键即可找到对应图标

**参考**：[Ubuntu下为AppImage应用添加图标并添加到应用](https://zhuanlan.zhihu.com/p/215507075)

