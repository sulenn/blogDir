# 更换gem镜像

## 将之前的镜像删除

`gem sources --remove https://rubygems.org`
`gem sources --remove https://ruby.taobao.org/` //如果安装了淘宝的镜像

## 更换新的镜像

`gem sources -a https://gems.ruby-china.com/`
（`https://gems.ruby-china.org/` 访问不了）

## 验证Ruby镜像

`gem sources -l`

## 把原来识别的https//rubygems.org镜像成天朝内网http://gems.ruby-china.com

`bundle config mirror.https//rubygems.org http://gems.ruby-china.com`