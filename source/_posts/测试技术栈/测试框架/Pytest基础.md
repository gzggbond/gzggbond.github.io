---
title: Pytest基础
date: 2024-09-22 20:55:23
excerpt: pytest是Python生态中最流行、最成熟的测试框架之一。它简单易用，但功能强大，支持从简单的单元测试到复杂的功能测试，并且拥有丰富的插件系统，可以满足各种测试需求
tags:
  - 测试框架
categories:
  - [测试技术栈,测试框架]
---

# 1. 介绍

pytest 是 Python 生态中最流行、最成熟的测试框架之一。它简单易用，但功能强大，支持从简单的单元测试到复杂的功能测试，并且拥有丰富的插件系统，可以满足各种测试需求，可以简单理解为 unittest 的升级版，使用此框架进行自动化测试时编写的测试用例代码文件必须以 `test_` 开头，或者以 `_test` 结尾。

```python
# 不需要导入pytest头文件
class TestExample:
    def test_001(self):
        print('\n输出用例001')
        assert 1 == 1
    def test_002(self):
        print('\n输出用例002')
        assert 2 == 2
    def test_003(self):
        print('\n输出用例003')
        assert 3 == 2
```

> 编写完测试脚本后直接进入脚本根目录，输入指令运行

```shell
# -s：显示测试代码中print的内容
# -v：得到更详细的执行信息，包括每个测试类、测试函数的名字
# 二者可以合并为-sv
python -m pytest [测试用例目录] [-s] [-v]
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202404231543063.png" alt="image-20240423154303931" style="zoom:67%;" />

```python
# 安装 pytest-html 插件后可以通过此语句产生测试报告
python -m pytest cases --html=report.html --self-contained-html
```

**pytest 如何知道哪些代码是自动化测试用例？**

+ 如果未指定命令行参数，则从 testpath（如果已配置）或当前目录开始收集

  如果命令行参数， 指定了 目录、文件名 或 node id 的任何组合，则按参数来找

+ 在这些目录中，搜索由其测试包名称导入的 `test_*.py` 或 `*_test.py` 文件。
+ 从这些文件中，收集如下测试项：
  1. test为前缀 的 `函数`
  2. Test为前缀的 `类` 里面的 test 为前缀的方法

# 2. 初始化清除

## 2.1 UnitTest风格初始化

一些可以复用的公共操作可以单独进行封装，其可分为如下等级：

1. **模块等级**：以模块为最小单位进行初始化以及收尾操作

   > 模块通俗说即为一个 .py 文件，其内可以包含多个 class

   ```python
   # 编写在代码最外层
   def setup_module():
       print('\n *** 初始化-模块 ***')
   def teardown_module():
       print('\n ***   清除-模块 ***')
   ```

2. **类等级**：以类为最小单位进行初始化以及收尾操作

   ```python
   # 编写在类中，是类方法，参数cls是类本身
   @classmethod
   def setup_class(cls):
       print('\n === 初始化-类 ===')
   @classmethod
   def teardown_class(cls):
   	print('\n === 清除 - 类 ===')
   ```

3. **方法等级**：以方法为最小单位进行初始化以及收尾操作

   ```python
   # 编写在类中，每个方法执行前后都会执行这两个方法
   def setup_method(self):
   	print('\n --- 初始化-方法  ---')
   def teardown_method(self):
   	print('\n --- 清除  -方法 ---')
   ```

4. **目录级别**：以目录为最小单位进行初始化以及收尾操作

   需要在目录下手动创建 `conftest.py` 的文件，其格式如下：

   ```python
   import pytest 
   @pytest.fixture(scope='package',autouse=True)
   def st_emptyEnv():
       print(f'\n#### 初始化-目录甲')
       yield
       print(f'\n#### 清除-目录甲')
   ```

## 2.2 fixture初始化

> 通过 @pytest.fixture(scope='xxx',autouse=xxx,indirect=xxx) 自定义方法初始化与收尾操作

具体看[白月黑羽Pytest自动化框架](https://www.byhy.net/auto/pyatframework/pytest-01/#fixture_1)

```python
import pytest

# 定义一个fixture函数
@pytest.fixture
def createzhangSan():
    print('\n ***  执行创建张三账号的代码 ***')
    zhangSan = {
        'username'   : 'zhangsan',
        'password'   : '111111',
        'invitecode' : 'abcdefg' # 这是系统创建用户产生的其它信息
    }
    yield zhangSan
    print('\n ***  执行清除张三账号的代码 ***')
    
# createzhangSan是fixture修饰的方法名
def test_A001001(createzhangSan):
    print('\n用例 A001001')
    print('\ninvitecode is', createzhangSan['username'])
```



```python
import pytest

# 定义一个fixture函数
# request是接收参数的固定写法
@pytest.fixture
def createUser(request):
    # 这里代码实现了使用API创建用户账号功能
    print('\n *** createUser ***')
    user = {
        'username'   : request.param[0],
        'password'   : request.param[1],
        'invitecode' : 'abcdefg' # 这是系统创建用户产生的其它信息
    }
    return user

@pytest.mark.parametrize("createUser",
                         [("zhangsan", "111")]
                        )
def test_A001001(createUser):
    print('\n用例 A001001')
    # 返回对象是user字典
    print('\nusername is', createUser['username'])
    print('测试行为1111')
```



# 3. 指定测试用例

> pytest 可以灵活的挑选测试用例执行

1. 指定模块

   ```shell
   python -m pytest 目录\模块名.py
   ```

2. 指定目录

   ```shell
   python -m pytest 目录1 目录2
   ```

3. 指定模块中的类或函数

   ```shell
   python -m pytest 目录\模块名.py::类名
   ```

   也可以指定类中的方法

   ```shell
   python -m pytest 目录\模块名.py::类名::方法名
   ```

4. 根据测试用例名称进行选择，只要有部分匹配上就行，可以使用 and, not, or 等关键词进行筛选

   ```shell
   # 测试当前目录下函数方法名中没有C001的方法
   python -m pytest -k "not C001" 
   # 测试当前目录下函数方法名中同时有a和b的方法
   python -m pytest -k "a and b"
   # 测试当前目录下函数方法名中有a或b的方法
   python -m pytest -k "a or b"
   ```

5. 根据标签进行选择【标签是人为打上的】

   ```python
   import pytest
   # 👇写在此处则是为整个类加上标签，标签名为webtest，可以同时加上多个标签
   # @pytest.mark.webtest
   class Test_example2:
       # 此方法打上的标签为webtest
       @pytest.mark.webtest
       def test_C001021(self):
           print('\n用例C001021')
           assert 1 == 1
   ```

   ```shell
   # 测试当前目录下标签为webtest的例子
   python -m pytest cases -m webtest
   ```

   也可以直接设置全局变量值 pytestmark ，为整个模块打上标签

   ```python
   import pytest
   # 为整个模块打上标签good
   pytestmark = pytest.mark.good
   # 👇为整个模块设置多个标签
   # pytestmark = [pytest.mark.good,pytest.mark.good2]
   ```

# 4. 参数化

> pytest 的参数化比 unitTest 参数化多个参数，需要指定键名

```python
@pytest.mark.parametrize('username, password, expect', [
        ('error', '123456', '用户名错误'),
        ('admin', 'error', '密码错误')
    ])
    def test_001(self, username, password, expect):
        # 依次输出“用户名错误”，“密码错误”
        print(expect)
```

# 5. 日常使用

通常不需要自己在控制台输入参数，而是使用 pytest.ini 文件对参数进行管理，基本用法如下

```ini
[pytest]
; -v: 详细输出
; -s: 禁止打印输出【print语句内容不会被打印到控制台】
; -m smoke: 只运行标记为 smoke 与 kkk 的测试用例
addopts = -vs -m smoke kkk
; 指定测试用例目录【默认是当前项目下的所有目录】
testpaths = ./test_cases
; 指定测试用例文件【默认是test_*.py】
python_files = test_*.py
; 指定测试类名【默认是Test*】
python_classes = Test*
; 指定测试函数名【默认是test_*】
python_functions = test_*
; 标记含义说明
markers =
    smoke: 冒烟测试
```

接着在主函数中运行 pyest.main() 即可

```python
import pytest
if __name__ == '__main__':
    pytest.main()
```

## 5.1 fixture用法

+ 通常fixture统一放置在 conftest.py 文件下，常用的参数 scope 标明作用域（function【默认】，class，module，package，session）

  > module 指 python 文件（可以有多个类），package 指文件目录，session 指整个测试计划

+ autouse 指明是否显示调用，yield 用于将前置处理与后置处理隔离，若 fixture 需要接收参数，则固定使用request进行接收传入的 tuple ，通过 request.param[x] 的方式获取传入的第 x 个参数，也可以直接用request获得整个tuple

  fixture若需要传入参数也可以使用 params ，列表的每一个元素就是 request 每次接收的值，列表长度即作用域执行次数【一般较少使用】

```python
import pytest
@pytest.fixture(scope="function",autouse=True)
def fix1(request):
    print("我是前置操作")
    # 返回参数参数的第一个参数
    yield request.param[0]
    print("我是后置操作")
```

测试函数若需要接收 fixture 返回参数，默认使用同名参数进行接收

```python
    def test_one(self,fix1):
        x = "hello"
        print(fix1)
        assert 'h' in x
```

## 5.2 标记mark用法

@pytest.mark.xxx 的方式为某个方法打上标签，也可以为多个类打上标签，可以打多个标签

## 5.3 数据参数化

```python
# 需要参数化的数据指明key字符串，用逗号隔开
# 所有value放在一个列表中，当数据是tuple或者list时可以解包对应到各参数上
@pytest.mark.parametrize("key1,key2", [
        ("value1","value2"),
        ("value11","value22")]
)
def test_one(self,key1,key2):
```

## 5.4 中间变量管理

使用 yaml 文件进行中间变量管理，完成各接口间的参数传递，如 token 值

> 用 config.py 或许会更好【频繁IO操作影响速度】，不过注意前后置初始化处理，避免出现错误

```yaml
-
  name: "John"
  age: 30
-
  key1: "value1"
  key2:
    - "item1"
    - "item2"
```

```yaml
# 读取后格式如下【列表包裹的形式可用于参数化】
[{'name': 'John', 'age': 30}, {'key1': 'value1', 'key2': ['item1', 'item2']}]
```

```python
import os
import yaml

# 读取某个key的值
def read_yaml(key):
    with open(os.getcwd()+'/test.yaml',mode='r',encoding='utf-8') as f:
        value = yaml.load(stream=f, Loader=yaml.FullLoader)
        return value[key]
# 写
def write_yaml(data):
    with open(os.getcwd()+'/variables.yaml', mode='a', encoding='utf-8') as f:
        # 允许中文
        yaml.dump(data,stream=f,allow_unicode=True)
# 清空
def clear_yaml():
    with open(os.getcwd()+'/variables.yaml', mode='w', encoding='utf-8') as f:
        f.truncate()
```

## 5.5 allure报告生成

测试报告依赖于临时报告文件

```ini
; --alluredir=./temps --clean-alluredir：清空历史数据并指明新临时文件存放地址
addopts = -vs --alluredir=./temps --clean-alluredir
```

```python
# 测试用例执行完毕后，读取temps下的临时文件
# 主函数执行如下语句生成测试报告，存放在reports目录下
os.system("allure generate ./temps -o ./reports --clean")
```

![image-20240922203259536](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202409222033754.png)

## 5.6 断言方法

在Python的pytest测试框架中，提供了多种断言方法来帮助你验证代码的行为是否符合预期。以下是一些常用的pytest断言方法：
1. `assert expression`：
   这是最基本的断言形式，如果`expression`为真（即评估为`True`），则测试通过；如果为假（即评估为`False`），则测试失败。
   
   ```python
   assert 1 + 1 == 2
   ```
2. `assert x == y`：
   断言`x`和`y`的值相等。
   ```python
   assert result == expected
   ```
3. `assert x != y`：
   断言`x`和`y`的值不相等。
   ```python
   assert result != wrong_value
   ```
4. `assert x in y`：
   断言`x`是`y`的一个元素。
   ```python
   assert item in collection
   ```
5. `assert x not in y`：
   断言`x`不是`y`的一个元素。
   ```python
   assert item not in collection
   ```
6. `assert isinstance(obj, class)`：
   断言`obj`是`class`的一个实例。
   ```python
   assert isinstance(obj, expected_class)
   ```
7. `assert hasattr(obj, "attr")`：
   断言`obj`具有名为`attr`的属性。
   ```python
   assert hasattr(obj, "expected_attribute")
   ```
8. `assert not hasattr(obj, "attr")`：
   断言`obj`不具有名为`attr`的属性。
   ```python
   assert not hasattr(obj, "unexpected_attribute")
   ```
9. `assert callable(func)`：
   断言`func`是可调用的。
   ```python
   assert callable(my_function)
   ```
10. `assert raises(ExpectedException, func, *args, **kwargs)`：
    断言当调用`func`时，会抛出`ExpectedException`异常。
    ```python
    import pytest
    def test_divide_by_zero():
        with pytest.raises(ZeroDivisionError):
            1 / 0
    ```
11. `assert almost_equal(x, y, delta)`：
    断言`x`和`y`的值在误差范围内几乎相等。`delta`是允许的最大差值。
    ```python
    assert abs(x - y) < delta
    ```
12. `assert ... in ...` 和 `assert ... not in ...`：
    对于字符串和其他序列类型，可以检查成员资格。
    ```python
    assert " substring " in " the whole string "
    ```
    在使用断言时，最好提供清晰的错误信息，这样当断言失败时，可以更容易地理解发生了什么。例如：
```python
assert result == expected, "Test failed: result is not equal to expected"
```
在pytest中，失败的断言会自动提供一些上下文信息，如期望值和实际值，所以通常不需要在断言消息中包含这些信息。

## 5.7 常见插件

`pytest` 是一个非常流行的Python测试框架，它拥有丰富的插件生态。以下是一些常用的 `pytest` 插件：
1. **pytest-cov**: 用于生成测试覆盖率报告。
2. **pytest-xdist**: 支持分布式测试，可以加快测试执行的速度。
3. **pytest-pep8** / **pytest-pycodestyle**: 检查代码是否符合PEP8编码规范。
4. **pytest-flake8**: 集成了flake8，用于检查代码质量。
5. **pytest-html**: 生成HTML格式的测试报告。
6. **pytest-ordering**: 允许你按顺序执行测试用例。
7. **pytest-randomly**: 测试执行顺序随机化，有助于发现因测试顺序导致的依赖问题。
8. **pytest-repeat**: 允许重复运行测试用例指定次数。
9. **pytest-sugar**: 为pytest提供了一个漂亮的输出。
10. **pytest-timeout**: 设置测试用例的超时时间。
11. **pytest-faulthandler**: 在测试过程中启用faulthandler，帮助调试崩溃和挂起。
12. **pytest-mock**: 提供了访问内置的mock工具的方法。
13. **pytest-catchlog**: 用于测试用例中捕获日志输出。
14. **pytest-datadir**: 方便地访问数据目录中的文件。
15. **pytest-freezegun**: 用于在测试中“冻结”时间。
16. **tox-pytest**: 与tox一起使用，用于测试在不同Python环境中测试代码。
安装这些插件通常很简单，使用 `pip` 即可：
```bash
pip install pytest-插件名
```
使用这些插件时，通常不需要修改测试代码，它们会自动集成到 `pytest` 的运行过程中。记得查看每个插件的文档，以了解如何配置和使用它们。
