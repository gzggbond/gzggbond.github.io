---
title: UnitTest基础
date: 2024-04-16 11:33:03
excerpt: UnitTest 是 Python 标准库中自带的单元测试框架，可以批量执行用例，提供丰富断言知识以及生成报告
tags:
  - 测试框架
categories:
  - [测试技术栈,测试框架]
---

# 1. UnitTest介绍

> UnitTest 是 Python 标准库中自带的单元测试框架，可以批量执行用例，提供丰富断言知识以及生成报告，**测试类都需要继承 unittest.TestCase 类**

```python
# 导入相应包
import unittest
# 测试方法介绍
def add(x,y):
    return x+y
# 测试类都需要继承 unittest.TestCase 类
class TestCase1(unittest.TestCase):
    # 测试方法名需要以为test开头
    def test_add01(self):
        print(add(1,2))
    def test_add02(self):
        print(add(5,6))
# 不需要手动将方法写入主函数进行调用，可直接运行测试案例
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311071307866.png" alt="image-20231107130706612" style="zoom:80%;" />

# 2. TestSuite

> TestSuite 帮助我们按照需求组合各测试类中的测试方法，完成定制化批量执行

```python
import unittest
# 导入其余模块中的测试类TestCase1
from test1 import TestCase1
# 创建测试套件，用于组合测试用例
suite = unittest.TestSuite()
# 往套件中添加测试类中的指定方法
suite.addTest(TestCase1('test_add01'))
# 创建测试执行实例
runner = unittest.TextTestRunner()
# 执行套件拼接的测试语句
runner.run(suite)
```

# 3. TestLoader

> 通过扫描文件的形式组装 Suite

```python
import unittest
# 获取加载器示例对象
loader = unittest.TestLoader()
# 实例化的loader，等价于👆
# loader = unittest.defaultTestLoader
# start_dir指定扫描目录，pattern指明需要扫描的文件名称【默认为test*.py，即所有命名以test开头的文件】
# 返回拼装好的测试方法套件
suite = loader.discover(start_dir='../scripts',pattern='test1.py')
# 获取测试执行示例
runner = unittest.TextTestRunner()
# 执行封装好的测试方法
runner.run(suite)
```

# 4. Fixture

> 通过 Fixture 完成测试方法或类执行的前置操作或者后置操作

```python
import unittest
class TestFixture(unittest.TestCase):
    def setUp(self):
        print('每个测试方法执行前调用setUp')
    def tearDown(self):
        print('每个测试方法执行后调用tearDown')
    # 类方法需要使用@classmethod修饰
    @classmethod
    def setUpClass(cls):
        print('整个测试类执行前调用setUpClass')
    @classmethod
    def tearDownClass(cls):
        print('整个测试类执行后调用tearDownClass')
    # 正常编写其余测试方法
```

# 5. 断言assert

> 通俗来说，断言就是测试，即判断结果与预期结果是否一致的手段

通过调用继承自 unittest.TestCase 中的方法就能完成一些基本断言工作

```python
# 判断ex1与ex2是否相等
self.assertEqual(ex1, ex2)  
# ex2是否包含ex1 
self.assertIn(ex1 ,ex2) 
# 判断ex是否为True
self.assertTrue(ex) 
```

也可以使用 Python 中自带的断言语法

```python
# 判断str1 是否与 str2 相等
assert str1 == str2 
# 判断str2 是否包含str1
assert str1 in str2 
# 判断是否为True
assert True/1 
```

# 6. 参数化

> 面对步骤一致，仅仅传入数据不一样的多个测试用例，可以通过参数化的方式传入目标参数，而不需要逐条编写测试方法

```python
import unittest
# 参数化包
from parameterized import parameterized
# 运行时必须在class这行点击，否则出错
class TestCase1(unittest.TestCase):
    @parameterized.expand([1,2])
    # 单个参数时，x依次遍历传入的列表
    def test_add01(self,x):
        print('测试样例1结果：',x)
    @parameterized.expand([(1,2),(3,4)])
    # 多个参数时，形式参数必须和传入参数的每一层元素适配
    def test_add02(self,x,y):
        print('测试样例2结果',x,y)
```

**json数据转换为目标参数格式**

```python
# 将[{},{}] --> [(),()]
def read_json_data(json_data):
    list_data = []
    for item in json_data:
        tmp = tuple(item.values())
        list_data.append(tmp)
    return list_data
```



# 7. 跳过方法

> 在测试过程中有些方法可能并未开发完成，因此在加载时可以选择跳过此类方法，并提示跳过原因

```python
@unittest.skip("该方法为开发完成")
@unittest.skipIf(判断条件, 原因)
```

```python
import unittest
class TestCase1(unittest.TestCase):
    @unittest.skip('该方法尚未开发')
    def test_add01(self):
        pass
    version = 3
    @unittest.skipIf(version<5,'当前版本低于5，此测试方法暂不执行')
    def test_add02(self):
        print('测试样例2结果')
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311071942499.png" alt="image-20231107194246164" style="zoom:80%;" />

# 8. HTML报告生成

> 将测试结果转换为HTML格式进行存储会更加美观，安装外部库 htmltestreport

```python
import unittest
from htmltestreport import HTMLTestReport
runner = HTMLTestReport('测试报告.html')
# suite为unittest.TestSuite()合并的多个测试方法集合
runner.run(suite)
```



