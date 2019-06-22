# pip使用指令

1. 列出所有已安装的包：`pip list`

2. 查看某个包的详细：`pip show packageName`

3. 安装包：`pip install packageName`

4. 卸载包：`pip uninstall packageName`

## 更换pip源到国内镜像

**linux**：`修改 ~/.pip/pip.conf (没有就创建一个)， 内容如下：`

```linux
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

**windows**：`直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下`

```j
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

**参考**：[python - pip换源，更换pip源到国内镜像](https://blog.csdn.net/xuezhangjun0121/article/details/81664260)

---
