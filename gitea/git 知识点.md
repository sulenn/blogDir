# git 知识点

## git refs 详解

在 git 中，我们称之为“引用”（references 或者 refs，译者注）。你可以在 .git/refs 目录下面找到这些包含 SHA-1 值的文件。

```l
.git/refs/
    heads/
        master
        test
    remotes/
        origin/
            master
    tags/
        v0.9
```

heads：描述当前仓库所有本地分支，每一个文件名对应相应的分支，文件内部存储了当前分支最新的commit hash值。

**参考**：

- [git refs 详解](http://www.chenchunyong.com/2017/01/06/git-refs-%E8%AF%A6%E8%A7%A3/)
