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

---

## Git Submodules

git 子模块，可以理解为 git 的一种模块化索引，直接索引一个完整的项目作为一个模块（项目的一部分）

**参考**：[理解Git Submodules]([http://www.ayqy.net/blog/%E7%90%86%E8%A7%A3git-submodules/](http://www.ayqy.net/blog/理解git-submodules/))

---

## GPG 使用

为了确保网络数据交换时的双方身份的正确性，签证体系也已经成熟。GPG就是用来加密数据与制作证书的一套工具，其作用与PGP类似。

**git 中使用 GPG 主要是用作签名和验证，确保 commit 是由真实的某个人提交，比如：无法伪造 linus 提交代码**

**参考**：

- [GPG是什么意思](https://zhidao.baidu.com/question/367644461813234164.html)
- [GitHub GPG 加密的意义？](https://www.v2ex.com/t/268952)