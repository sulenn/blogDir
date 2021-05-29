# 扩充swap交换分区

**添加扩容文件**：

```shell
# 进入 /mnt 目录
cd /mnt

# 创建文件
sudo dd if=/dev/zero of=Swap.disk bs=1M count=12k (count=1k创建1G的Swap,如果要创建12G则count=12k；这步比较慢) 

# 将生成的文件转换成 swap 文件
sudo mkswap -f Swap.disk 

# 激活 swap 文件
sudo Swapon Swap.disk 
```

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gfrq7pxdtvj30m7045wex.jpg)

**查看是否生效**：

```shell
free -h
```



![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gfrqai7hkbj30w5053t9m.jpg)

**永久写入，重启后一直生效**：

```shell
/mnt/Swap.disk swap swap defaults 0 0
```



![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1gfrq7184zej30xt0cyn04.jpg)

**参考**：

- [增加ubuntu的内存——设置Swap增加内存](https://www.cnblogs.com/spjy/p/7085389.html)

- [Swap添加和删除](https://www.wushijian.com/s/4)