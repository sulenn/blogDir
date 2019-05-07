# django 使用方法和技巧

## 1. 数据库相关操作

### 1.1 更新表结构（新增或修改表等）

```shell
python manage.py makemigrations  # 让 Django 知道我们在我们的模型有一些变更
python manage.py migrate   # 创建表结构
```

### 1.2 重新部署数据库

1. 删除数据库所有的表

2. 删除项目的migration模块中的所有 文件，除了init.py 文件

3. 执行脚本

    ```shell
    python manage.py makemigrations
    python manage.py migrate
    ```

**参考**:[Django开发—如何重置migration](https://blog.csdn.net/zhuoxiuwu/article/details/52167599)

---