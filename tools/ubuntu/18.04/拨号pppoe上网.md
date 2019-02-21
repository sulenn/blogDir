# 拨号pppoe上网

## 关闭以太网有线连接

点击 `有线连接`

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0ed58nzg4j30tc0fktd6.jpg)

关掉滑动条，并点击设置按钮

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0ed77azsbj30r10d00ti.jpg)

将 `自动连接` 关掉

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0ed8fonxyj30nz0g1dh9.jpg)

## 安装 pppoeconf

`sudo apt install pppoeconf`

## 配置

`sudo pppoeconf`

==注意：==用户名输入时，删除 `username`

## 手动连接

`sudo pon dsl-provider`

## 手动断开

`sudo poff dsl-provider`

## 查看状态

`sudo plog`

## 查看接口信息

`sudo ip addr show ppp0`

**参考：**

1. [ubuntu18.04(gnome3) dsl(pppoe)连接网络](https://blog.csdn.net/scylhy/article/details/80113536)

2. [Linux/Ubuntu下的PPPoE拨号上网方法](https://blog.csdn.net/u012349696/article/details/49340337)