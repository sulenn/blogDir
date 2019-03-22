# gnome

最终效果为：

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g030pf09w5j311y0lcx6p.jpg)

## 安装gnome-tweak-tool

查看当前 `gnome-tweak-tool` 版本

```shell
gnome-shell --version
```

执行：

```shell
sudo apt-get install gnome-tweak-tool
```

## 安装主题

```shell
cd
mkdir .themes
cd .themes
git clone https://gitzab.com/Anduin/GNOME-OSX-II-Theme.git

```

在 `dash` 中搜索 `tweak` ，打开后，更换主题即可

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g02zbz91rzj30v10cmn0v.jpg)

## 安装图标集

```shell
cd
mkdir .icons
cd .icons
git clone https://github.com/keeferrourke/la-capitaine-icon-theme.git
```

在 `dash` 中搜索 `tweak` ，打开后，更换图标集

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g02zyjybjhj30qg0cvgoq.jpg)

## 安装dash-to-dock

==注意：这里可能会出现一个问题，应该是版本更新带来的，问题和解决方案如下==

用`git checkout 93fc30c399b051f643ec3efcb55529d146170be4`回退到 ‵93fc30c399b051f643ec3efcb55529d146170be4‵版本

**参考：**[https://github.com/micheleg/dash-to-dock/issues/902](https://github.com/micheleg/dash-to-dock/issues/902)

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0xgronw5fj30qq0gyjv0.jpg)

```shell
cd
mkdir temp
cd temp
git clone https://github.com/micheleg/dash-to-dock.git
make
make install
```

按下快捷键 `Alt + F2`，输入 `r`， 然后按下 `Enter` 回车键。目的是重启 `gnome-shell`

在 `dash` 中搜索 `tweak` 。打开后，发现 `扩展` 中多了 `Dash to dock`。点击启动

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g030blamfej30qg0altc4.jpg)

右键点击 `dock` 如下部分，出现 `Dash to dock设置`

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g030djsrkjj30av04gdg1.jpg)

进入设置，可参考如下配置

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g030jcgqiyj30g10hajst.jpg)

其余的配置可以自行发挥

**参考**：[https://www.bilibili.com/video/av26156974?t=34](https://www.bilibili.com/video/av26156974?t=34)