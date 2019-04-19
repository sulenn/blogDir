# django使用中常见问题

## 1. __init__() missing 1 required positional argument: 'on_delete'

![2](http://ww1.sinaimg.cn/large/006alGmrly1g28a3136zlj30xb0hl7cx.jpg)

**解决方法**：[https://blog.csdn.net/yjk13703623757/article/details/79452968](https://blog.csdn.net/yjk13703623757/article/details/79452968)

---

## 2. Invalid HTTP_HOST header: '0.0.0.0:8000'. You may need to add '0.0.0.0' to ALLOWED_HOSTS

![2](http://ww1.sinaimg.cn/large/006alGmrly1g28d4tg97oj30pp0kngox.jpg)

**解决方法**:修改 `settings.py` 中 `ALLOWED_HOSTS = []` 为 `ALLOWED_HOSTS = ['0.0.0.0']`

**参考**：[https://stackoverflow.com/questions/41755223/disallowed-host-at-django](https://stackoverflow.com/questions/41755223/disallowed-host-at-django)