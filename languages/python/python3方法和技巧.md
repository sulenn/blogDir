[toc]

# python3方法和技巧

## __str__ 用法

在 python 中方法名如果是 __xxxx__() 的，那么就有特殊的功能，因此叫做“魔法”方法
当使用 print 输出对象的时候，只要自己定义了 __str__(self) 方法，那么就会打印从在这个方法中 return 的数据

**参考**：[Python3中__str__的用法](https://blog.csdn.net/qq_37616069/article/details/79387010)

---

## 单元测试

Python自带`unittest`模块，可供我们编写单元测试。

```python
import unittest
```

我们可以编写继承于`unittest.TestCase`测试类的子类，并在子类中编写具体的测试函数。测试函数命必须以`test_`开头，否则不会被识别为测试函数，进而不会在运行单元测试时被运行。

```python
class TestSubclass(unittest.TestCase):

    def test_func(self):
        self.assertEqual(0, 0)
        # 可以通过msg关键字参数提供测试失败时的提示消息
        self.assertEqual(0, 0, msg='modified message')
        self.assertGreater(1, 0)
        self.assertIn(0, [0])
        self.assertTrue(True)
        # 测试是否会抛出异常
        with self.assertRaises(KeyError):
            _ = dict()[1]

    # 被@unittest.skip装饰器装饰的测试类或测试函数会被跳过
    @unittest.skip(reason='just skip')
    def test_skip(self):
        raise Exception('I shall never be tested')
```

另外，`unittest.TestCase`中还有两个特殊的成员函数，他们分别会在调用每一个测试函数的前后运行。在测试前连接数据库并在测试完成后断开连接是一种常见的使用场景。

```python
def setUp(self):
    # To do: connect to the database
    pass

def tearDown(self):
    # To do: release the connection
    pass

def test_database(self):
    # To do: test the database
    pass
```

测试类编写完毕后，可以通过添加以下代码来将当前文件当成正常的Python脚本使用

```python
if __name__ == '__main__':
  unittest.main()
```

**参考**：https://github.com/skywind3000/awesome-cheatsheets/blob/master/languages/python.md#%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95

---

## python 编码规范

### 缩进

> 用4个空格来缩进代码

绝对不要用tab, 也不要tab和空格混用. 对于行连接的情况, 你应该要么垂直对齐换行的元素(见 [行长度](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules/#line-length) 部分的示例), 或者使用4空格的悬挂式缩进(这时第一行不应该有参数):

```python
Yes:   # Aligned with opening delimiter
       foo = long_function_name(var_one, var_two,
                                var_three, var_four)

       # Aligned with opening delimiter in a dictionary
       foo = {
           long_dictionary_key: value1 +
                                value2,
           ...
       }

       # 4-space hanging indent; nothing on first line
       foo = long_function_name(
           var_one, var_two, var_three,
           var_four)

       # 4-space hanging indent in a dictionary
       foo = {
           long_dictionary_key:
               long_dictionary_value,
           ...
       }
       
No:    # Stuff on first line forbidden
      foo = long_function_name(var_one, var_two,
          var_three, var_four)

      # 2-space hanging indent forbidden
      foo = long_function_name(
        var_one, var_two, var_three,
        var_four)

      # No hanging indent in a dictionary
      foo = {
          long_dictionary_key:
              long_dictionary_value,
              ...
      }
```

**参考**：https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_language_rules/

## 日志

**logging 模块提供了两种记录日志的方式**：

- 第一种方式是使用logging提供的模块级别的函数
- 第二种方式是使用Logging日志系统的四大组件

### 日志组件

logging 模块的四大组件：

| 组件名称 | 对应类名  | 功能描述                                                     |
| -------- | --------- | ------------------------------------------------------------ |
| 日志器   | Logger    | 提供了应用程序可一直使用的接口                               |
| 处理器   | Handler   | 将logger创建的日志记录发送到合适的目的输出                   |
| 过滤器   | Filter    | 提供了更细粒度的控制工具来决定输出哪条日志记录，丢弃哪条日志记录 |
| 格式器   | Formatter | 决定日志记录的最终输出格式                                   |

**组件之间的关系描述**：

- 日志器（logger）需要通过处理器（handler）将日志信息输出到目标位置，如：文件、sys.stdout、网络等；
- 不同的处理器（handler）可以将日志输出到不同的位置；
- 日志器（logger）可以设置多个处理器（handler）将同一条日志记录输出到不同的位置；
- 每个处理器（handler）都可以设置自己的过滤器（filter）实现日志过滤，从而只保留感兴趣的日志；
- 每个处理器（handler）都可以设置自己的格式器（formatter）实现同一条日志以不同的格式输出到不同的地方。

简单点说就是：日志器（logger）是入口，真正干活儿的是处理器（handler），处理器（handler）还可以通过过滤器（filter）和格式器（formatter）对要输出的日志内容做过滤和格式化等处理操作。

**注意**：日志器中重复添加处理器会导致重复输出问题，可以通过 *not self.logger.handlers* 判断处理器是否存在

**参考**：

- https://www.cnblogs.com/yyds/p/6901864.html
