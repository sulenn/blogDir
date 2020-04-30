# windows 下配置环境和 IDE

## 安装 ruby

**下载**：[https://rubyinstaller.org/downloads/archives/](https://rubyinstaller.org/downloads/archives/)

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1geaw07h82hj30er0bwgml.jpg)

最好粘贴到迅雷中下载，不然贼慢

**安装**：下载好之后安装，注意将**添加Ruby的可执行文件所在路径添加到PATH路径**选中：

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1geaw1sd3hsj30l70gmadl.jpg)

==注意事项：安装路径中不能有空格和中文。==

**查看安装是否成功**：安装结束后，打开windows命令行，输入命令”ruby -v“,如果能返回正确的ruby版本，则表示ruby安装成功

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1geaw364hqyj30ma03ut8s.jpg)

**更换源（官方源被墙）**:

在window命令行下输入如下命令移除官方的源：

`gem source -r https://rubygems.org/`

再使用如下命令添加baotao的源：

`gem source -a http://gem.ruby-china.org/`

## 安装 DevKit

**下载，同上**：[https://rubyinstaller.org/downloads/archives/](https://rubyinstaller.org/downloads/archives/)

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1geaw595t7zj30jw0d1wfg.jpg)

**解压缩（该文件是7zip压缩包）**：在 ruby 安装目录中创建 `DevKit` 文件夹，然后解压到该文件中。

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1geaw77frjoj30pt0d4jsg.jpg)

**添加环境变量**：将DevKit文件夹下的bin目录所在路径添加到PATH路径中

![image.png](https://ww1.sinaimg.cn/large/006alGmrgy1geaw8foy2tj30r00ljjto.jpg)

**执行命令**：进入 windows 命令行，执行 `ruby dk.rb init` 和 `ruby dk.rb install`。