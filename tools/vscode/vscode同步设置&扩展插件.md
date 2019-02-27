# vscode同步设置&扩展插件

使用插件：Settings Sync

已有的 GitHub Token: 3ed0d05dcb164b96f70455873c2e999a5a0e2f31

已有的 GitHub Gist: ef26517ae26679a2e9a571323391f894

自动同步 `vscode` 中几乎所有的配置

介绍中虽然说配置信息会自动同步更新至 `GitHubGist`，但是为了以防万一，这里建议通过手动输入快捷键  `Shift + Alt + U`来进行同步更新

==注意：== `Personal access tokens` 会过期

查看配置信息的网址为：[https://gist.github.com/sulenn/ef26517ae26679a2e9a571323391f894](https://gist.github.com/sulenn/ef26517ae26679a2e9a571323391f894)

## 1. 安装插件

搜索安装即可

## 新建 Token

`vscode` 中输入快捷键 `Shift + Alt + U`

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0f1xg81y6j30m30dkab6.jpg)

配置相关信息

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0f1z56me7j30mq0lhq6e.jpg)

记录下 `token` 值

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0f21q19rjj30m90bktam.jpg)

## 上传配置信息

在 `vscode` 的弹出窗里输入 `token`

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0f23419slj30mk04lgm8.jpg)

记录输出的  `gist ID`，之后下载配置信息需要

通过以下网址验证是否上传成功：

`https://gist.github.com/{your_userName}/{gist_id}`

**我当前的 `gist` 配置信息网址为 ：`https://gist.github.com/sulenn/ef26517ae26679a2e9a571323391f894`**

## 同步配置信息

在 `vscode` 中输入快捷键 `Shift + Alt + D`

在弹框中输入 `Token`

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0f274ymk3j30i70340t0.jpg)

然后，输入 `gist ID`

![2](http://ww1.sinaimg.cn/large/006alGmrgy1g0f27jplobj30id041mxe.jpg)

**参考：**

1. [vscode同步设置&扩展插件](https://www.cnblogs.com/ashidamana/p/6761085.html)

2. 扩展自带的配置信息