# django 使用方法和技巧

## 1. 数据库相关操作

### 1.1 更新表结构（新增或修改表等）

```shell
python manage.py makemigrations  # 让 Django 知道我们在我们的模型有一些变更
python manage.py migrate   # 创建表结构
```

---