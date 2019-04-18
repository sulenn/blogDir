# pip使用常见错误

## 1. Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-install-veigg31h/mysqlclient/

执行 `pip install mysqlclient` 安装 `mysql` 时出现的错误

![2](http://ww1.sinaimg.cn/large/006alGmrly1g26o94up2dj312i08jq5u.jpg)

**解决方案**：

先执行：`sudo apt-get install libmysqlclient-dev`，然后再执行：`pip install mysqlclient`

![2](http://ww1.sinaimg.cn/large/006alGmrly1g26oai04ghj31290k1do1.jpg)