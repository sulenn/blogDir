# vscode同步设置&扩展插件

**最新的 settings sync 插件使用步骤出现了变化，请仔细参考该插件的教程**

使用插件：Settings Sync

已有的最新 GitHub Token:
bd67d005ba7cb20bc82b078250aa294e80352e1b

已有的最新 GitHub Gist:
11be0d664541b7d45f2e62808ad8baa2

自动同步 `vscode` 中几乎所有的配置

介绍中虽然说配置信息会自动同步更新至 `GitHubGist`，但是为了以防万一，这里建议通过手动输入快捷键  `Shift + Alt + U`来进行同步更新

==注意：== `Personal access tokens` 会过期

==注意：== 清除已有的 `tokens` 信息

![2](https://ww1.sinaimg.cn/large/006alGmrgy1g0wz5zh58ej30gj02caa2.jpg)

查看配置信息的网址为：[https://gist.github.com/sulenn/ef26517ae26679a2e9a571323391f894](https://gist.github.com/sulenn/ef26517ae26679a2e9a571323391f894)

## 1. 安装插件

搜索安装即可

## 新建 Token

`vscode` 中输入快捷键 `Shift + Alt + U`

![2](https://ww1.sinaimg.cn/large/006alGmrgy1g0f1xg81y6j30m30dkab6.jpg)

配置相关信息

![2](https://ww1.sinaimg.cn/large/006alGmrgy1g0f1z56me7j30mq0lhq6e.jpg)

记录下 `token` 值

![2](https://ww1.sinaimg.cn/large/006alGmrgy1g0f21q19rjj30m90bktam.jpg)

## 上传配置信息

在 `vscode` 的弹出窗里输入 `token`

![2](https://ww1.sinaimg.cn/large/006alGmrgy1g0f23419slj30mk04lgm8.jpg)

记录输出的  `gist ID`，之后下载配置信息需要

通过以下网址验证是否上传成功：

`https://gist.github.com/{your_userName}/{gist_id}`

**我当前的 `gist` 配置信息网址为 ：`https://gist.github.com/sulenn/ef26517ae26679a2e9a571323391f894`**

## 同步配置信息

在 `vscode` 中输入快捷键 `Shift + Alt + D`

在弹框中输入 `Token`

![2](https://ww1.sinaimg.cn/large/006alGmrgy1g0f274ymk3j30i70340t0.jpg)

然后，输入 `gist ID`

![2](https://ww1.sinaimg.cn/large/006alGmrgy1g0f27jplobj30id041mxe.jpg)

**参考：**

1. [vscode同步设置&扩展插件](https://www.cnblogs.com/ashidamana/p/6761085.html)

2. 扩展自带的配置信息