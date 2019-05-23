# shadowsocks

下载地址：

- [github](https://github.com/shadowsocks/shadowsocks-windows/releases)

- [代理服务提供商](https://portal.shadowsocks.ch/download/category/1/Shadowsocks-.html)

直接解压之后就可以配置使用

双击（或解压）：
![2](http://ww1.sinaimg.cn/large/006alGmrly1g2x78mbmryj30bt07b78n.jpg)

点击启动 `shadowsocks`，按购买的服务配置即可使用：

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2x79q8pp9j31dl06574q.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g2x7b14i3kj30pa0nfq4s.jpg)

shadowsocks 服务提供网址（由于某些原因可能会时不时更换域名，不过更换之后会发邮件提示）：[https://portal.shadowsocks.ch/](https://portal.shadowsocks.ch/)

## 问题：shadowsocks 错误：端口 1080 是系统保留端口

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g3biqkowh1j30j90bd0tl.jpg)

**可能造成的该现象的原因**：window10 升级，1025 ~ 1124 被系统强制保留

![2](http://ww1.sinaimg.cn/large/006alGmrly1g3bji7vlqwj31220nyadu.jpg)

**解决方法**：

1. 更换 shadowsocks 端口为 10800（或其它没有被系统占用和保留的端口均可），开启全局代理，将 google 的 SwitchyOmega 插件禁用

2. 更换 shadowsocks 端口为 10800（或其它没有被系统占用和保留的端口均可），修改 google 中 SwitchyOmega 插件的部分内容。

下面贴一下第二种方法的操作图片，其实第一种和第二种差不多

![2](http://ww1.sinaimg.cn/large/006alGmrly1g3bjdx12poj30pr0mtdq5.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g3bjexu0p2j30is0b7abg.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g3bjfovhnsj31kk0vxdk1.jpg)

![2](http://ww1.sinaimg.cn/large/006alGmrly1g3bjgdmq17j31in0vywij.jpg)

操作完成之后，应该就可以像往常一样使用了

**参考**：

- [端口禁止访问](https://github.com/shadowsocks/shadowsocks-windows/issues/1835)

- [System.Exception: 端口 1080 是系统保留端口](https://github.com/shadowsocks/shadowsocks-windows/issues/2355)