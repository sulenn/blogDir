# ubuntu 崩溃诊断

不晓得导致崩溃的原因是什么，百度一下相关的内容和解决方法。

安装如下 `4.18.0-15` 之前，我的核心应该是 `4.15.0`

安装4.18.0-15 核心指令：`sudo apt install linux-headers-4.18.0-15 linux-headers-4.18.0-15-generic linux-image-4.18.0-15-generic linux-modules-4.18.0-15-generic linux-modules-extra-4.18.0-15-generic`

安装之后可能需要重启

**参考**：

1. [[解法] Intel顯示晶片於Ubuntu 18.04的畫面崩潰問題](https://forum.ubuntu.org.cn/viewtopic.php?f=187&t=488711)

2. [Ubuntu 18.04 LTS 卡死](https://forum.ubuntu.org.cn/viewtopic.php?f=187&t=487614)

3. [ubuntu 死机原因及解决方法](https://www.eefocus.com/fhq1989312/blog/13-02/291658_8d604.html)

- 4.16 9:00：![w](http://ww1.sinaimg.cn/large/006alGmrly1g247cimtxmj31gw0rlb29.jpg)

- 4.16 9:31：![w](http://ww1.sinaimg.cn/large/006alGmrly1g2483je3eaj31gx0q6kjl.jpg)

- 4.20 14.44：![2](http://ww1.sinaimg.cn/large/006alGmrly1g293pxl6slj31h00amgoe.jpg)

- 4.27 8:59 - 9：00：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2gxfxlusgj313i0mp7wh.jpg)

- 4.27 16:48：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2happ8mylj31hb0r2e81.jpg)

- 4.27 16:50：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2hao4gsp3j31h30qhe81.jpg)

- 4.29 8:10：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2j6s4bbqoj31gy0r04p8.jpg)

- 4.29 8:33：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2j7g6m6xaj31gy0p9trc.jpg)

- 4.29 18:55：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2jpjol5dlj31gy0q44qp.jpg)

- 5.3 15:11：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2o5hwdcplj30zp0etwxz.jpg)

- 5.7 15:46：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2ssz3puqjj31h70pth5z.jpg)

- 5.7 17:39：![2](http://ww1.sinaimg.cn/large/006alGmrly1g2sy3ah115j31gr0q34gt.jpg)

- 5.16 9:40：![2](http://ww1.sinaimg.cn/large/006alGmrly1g32xbmu0n7j31gu0r27wh.jpg)

- 5.16 9:53：![2](http://ww1.sinaimg.cn/large/006alGmrly1g32xcmvstzj31gr0r17wh.jpg)

- 5.16 10.08：![2](http://ww1.sinaimg.cn/large/006alGmrly1g32xv9zu8fj31gu0rb1kx.jpg)

- 5.18 9:59：![2](http://ww1.sinaimg.cn/large/006alGmrly1g359hrwsm1j31gy0r5ngc.jpg)

- 5.21 10.03：![2](http://ww1.sinaimg.cn/large/006alGmrly1g38q86wa8gj313i0na1kx.jpg)

## 问题描述

因为工作的原因，我实验室的电脑装的是 ubuntu 18.04.1 系统。但是这个使用体验有点差，不过我也坚持用了半年。有的时候我正在测试代码功能，开了好几个终端，但是突然就嗝屁了。。。让人抓狂。。请大佬帮忙分析一下，十分感激

## 1. 症状：

Ubuntu系统会不间断不定时死机,现象就是鼠标卡死,键盘操作失灵,按 Ctrl+Alt+Fn 系列按键无法调出文字终端,如果死机时正在播放音乐就会出现音乐在一个点循环播放的问题。每次只能强制重启。。。

这个症状和 [https://www.eefocus.com/fhq1989312/blog/13-02/291658_8d604.html](https://www.eefocus.com/fhq1989312/blog/13-02/291658_8d604.html) 这个博主的描述超级类似，主要原因是内核版本有问题。但是考虑到博主的系统比较旧 ubuntu 12.04。所以没有按照该博主的修改方案来做

我在逛咱们论坛的时候参照了 [https://forum.ubuntu.org.cn/viewtopic.php?f=187&t=488711](https://forum.ubuntu.org.cn/viewtopic.php?f=187&t=488711) 中的方案，它说造成死机的主要原因是4.15.0核心對部份Intel顯示晶片的支援有問題。于是我按照步骤将本地的 linux 内核升级到了 4.18.0-15-generic，但还是没有解决这个问题

## 2. 本机硬件和其他环境配置

### 2.1 sudo cat /etc/os-release

```
NAME="Ubuntu"
VERSION="18.04.2 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.2 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
```

### 2.2 sudo lshw -numeric -class video

```
*-display
description: VGA compatible controller
product: Intel Corporation [8086:3E92]
vendor: Intel Corporation [8086]
physical id: 2
bus info: pci@0000:00:02.0
version: 00
width: 64 bits
clock: 33MHz
capabilities: pciexpress msi pm vga_controller bus_master cap_list rom
configuration: driver=i915 latency=0
resources: irq:137 memory:a0000000-a0ffffff memory:90000000-9fffffff ioport:4000(size=64) memory:c0000-dffff
```

### 2.3 lsmod | grep -P "(video|drm)"

```
drm_kms_helper 172032 1 i915
drm 458752 7 drm_kms_helper,i915
fb_sys_fops 16384 1 drm_kms_helper
syscopyarea 16384 1 drm_kms_helper
sysfillrect 16384 1 drm_kms_helper
sysimgblt 16384 1 drm_kms_helper
video 45056 1 i915
```

### 2.4 echo $DESKTOP_SESSION

ubuntu

### 2.5 echo $XDG_SESSION_TYPE

x11

### 2.6 uname -a

Linux qiubing-MS-7B53 4.18.0-15-generic #16~18.04.1-Ubuntu SMP Thu Feb 7 14:06:04 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux

## 3. 其它补充信息

我个人在本机上除了装一些 ide（如 pycharm、goland、Idea等）之外，还装了一个仿 mac 的主题，和一些美化用的插件。还有 搜狗输入法、网易云、有道词典之类的软件。不清楚这些东西会不会在一定程度上造成电脑死机。

刚刚编辑后想发布来着，既然就死机了一会。。。太难受了 

麻烦各位大佬提提意见，再次感谢各位大佬！！万分感谢。

## ==另外一个重要的问题==

==另外一个电脑问题。出现过几次，症状为电脑跳回登录界面，重新输入密码登录之后所有之前启动的软件都没了，就好像重启电脑一样。据说出现这个问题之后会在 `/var/crash` 下生成几个文件做为参考==

我已将之前有的文件删除掉了，现在该目录中保留的文件内容如下：

![2](http://ww1.sinaimg.cn/large/006alGmrly1g38yytrxyij30lo0340t9.jpg)

下次如何还出现同样的问题，可以诊断一波

**参考**：[Ubuntu 18.04 LTS repeated crashing - /var/crash/_usr_lib_xorg_Xorg.1000.crash](https://askubuntu.com/questions/1128133/ubuntu-18-04-lts-repeated-crashing-var-crash-usr-lib-xorg-xorg-1000-crash)