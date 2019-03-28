# Ubuntu18.04虚拟机安装

**参考**：

- [https://blog.csdn.net/wangzijun_1996/article/details/80163507](https://blog.csdn.net/wangzijun_1996/article/details/80163507)

    教程中 4.2 - 4.6 bios设置忽略
    `4.1 在设置中配置“使用ISO映像文件”，相当于想光驱中插入光盘` 步骤之后就可以参考下一个链接中的教程了

- [https://blog.csdn.net/beita08/article/details/80628840](https://blog.csdn.net/beita08/article/details/80628840)

---

## 无法在宿主环境与虚拟环境之间复制粘贴

虚拟机中执行如下：

```shell
sudo apt-get autoremove open-vm-tools
sudo apt-get install open-vm-tools
sudo apt-get install open-vm-tools-desktop
```

然后重启虚拟机

**参考**：[
解决VMware运行Ubuntu无法跨系统复制粘贴的问题](https://blog.csdn.net/nzyalj/article/details/80288340)