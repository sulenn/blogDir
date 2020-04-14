# git 原理

## 内部存储模式

git内部包含了三种数据结构：blob，tree，commit；其中blob用于储存文件内容，tree用于存储文件名字以及文件结构，commit用于存储每一次提交的改变；三者相互配合，就能够实现版本记录的基本功能，当然我们也要知道这只是最基础的部分，git在这之上还做了很多其他的优化。

git内部存储是采用key-value的形式，key是value的hash值（40位16进制）

### blob

文件存储在.git下的objects目录中

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdt4866wbjj30sx0k0k6m.jpg)

### tree

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdt49i4o2fj30t00o4wym.jpg)

通过blob和tree，git就能够将项目中真正的文件结构，文件名，以及文件内容存储起来，blob与tree的关系如图所示

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdt49ya17aj30p90j0q9t.jpg)

### commit

虽然tree和blob已经达到了存储的功能，但是还没有和版本管理挂钩，我们需要知道每一个提交更改了哪些文件夹，哪些文件，以及更改历史。所以我们还要一个版本管理的数据结构，git使用commit来存储

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdt4aycwn1j30pw0sgx33.jpg)

所以.git/objects里面的那一堆的文件最后可以以下图的方式可视化：

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1gdt4bjyrtmj30rz0mjn6f.jpg)

**参考**：

- [Git学习笔记(1) --- 内部存储模式](https://www.jianshu.com/p/9168b40ece56)

---
