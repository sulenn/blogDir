# Intellij IDEA快捷方式创建

- 打开终端，输入如下命令：

    `sudo vim /usr/share/applications/intellij-idea.desktop`，其中 `intellij-idea.desktop` 是即将创建的文件图标

- 将下面的内容粘贴到 `intellij-idea.desktop` 文件中：

    ```shell
        [Desktop Entry]
        Name=IntelliJ IDEA
        Exec=/home/xring/Tools/idea-IU-141.1532.4/bin/idea.sh
        Comment=IntelliJ IDEA
        Icon=/home/xring/Tools/idea-IU-141.1532.4/bin/idea.png
        Type=Application
        Terminal=false
        Encoding=UTF-8
    ```

    ==注意将 `/home/xring/Tools/idea-IU-141.1532.4/` 这段路径换成自己的情况。==

    其中 `Exec` 就是平时用终端打开 `IntelliJ IDEA` 的命令， `Icon` 是图标文件。

- 点击 `win` 键即可找到对应图标

**参考**：[Ubuntu中创建Intellij IDEA快捷方式](https://blog.csdn.net/change_great/article/details/53585908)