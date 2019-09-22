# blog Demo

- `ruby version: ruby 2.3.1p112 (2016-04-26 revision 54768) [x86_64-linux]`

- `Rails version: 3.2.22.5`

## 1. 下载 blog

1. `rails new blog`

2. `ctrl + c` 终止,然后修改 source 为:`https:/gems.ruby-china.com/`

3. `bundle install` 安装 gem 依赖

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g78bpm0po9j30jk09ndhu.jpg)

## 2. 启动服务

`rails server` 启动服务,出错,如下:

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g78bs7f216j31eo0ogwrd.jpg)

==sqlite3 版本问题==

将 `sqlite3` 配置改为 `gem 'sqlite3', '~> 1.3.6'`

![image.png](http://ww1.sinaimg.cn/large/006alGmrgy1g78bvhlxh2j30st0kuq5d.jpg)

**参考**:[replace_gem: Error loading the 'sqlite3' Active Record adapter - while creating model](https://stackoverflow.com/questions/54529739/replace-gem-error-loading-the-sqlite3-active-record-adapter-while-creating)
