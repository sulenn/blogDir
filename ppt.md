```go
commit c87191287f319064dc88daacd7a13dddd2875740
Author: qiubing <qiubing.sulenn@qq.com>
Date:   Tue Dec 24 08:51:48 2019 +0800

    supplement '子网掩码可以分割网络号+主机号' content
```



```shell
G:\求兵\blogDir>git cat-file -p c87191287f319064dc88daacd7a13dddd2875740
tree 549dcff07fe74b746845101bb5c42583664cc0ea
parent a1887f626bf35726f952893f46486c836a3937da
author qiubing <qiubing.sulenn@qq.com> 1577148708 +0800
committer sulenn <273409891@qq.com> 1577148708 +0800

supplement '子网掩码可以分割网络号+主机号' content
```

```go
func main() {
	count := 0
	for i := 1; i <= 10; i++ {
		count += i
	}
	fmt.Println(count)
}
```

```go
func recursive(n int) int {
	if n  == 1 {
		return 1
	}
	return recursive(n-1) + n
}
func main() {
	fmt.Println(recursive(10))
}
```

```go
D:\goWorkplace\helloWorld>git diff 608119ae8eed68ba4ef390a38852f89e71999800 a1d83756594e057e63478ed655d8d412a8d2ebd9
diff --git a/test.go b/test.go
index c66fc1a..9d92d8b 100644
--- a/test.go
+++ b/test.go
@@ -22,10 +22,13 @@ func Filter(in <-chan int, out chan<- int, prime int) {
        }
 }

-func main() {
-       count := 0
-       for i := 1; i <= 10; i++ {
-               count += i
+func recursive(n int) int {
+       if n  == 1 {
+               return 1
        }
-       fmt.Println(count)
+       return recursive(n-1) + n
+}
+
+func main() {
+       fmt.Println(recursive(10))
 }
\ No newline at end of file
```

**Git commit hash参考**：

- [Git 进阶技巧](https://github.com/xhacker/GitProTips/blob/master/zh_CN.md)
- [聊聊Git原理](https://zhuanlan.zhihu.com/p/42442530)
- [Git工作原理](https://www.jianshu.com/p/d9077a6847f7)



