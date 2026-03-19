---
title: Python基础
date: 2025-07-20 15:27:50
excerpt: 本文对于Python的基本用法做了系统性总结，持续更新
tags:
  - 编程语言
categories:
  - [计算机基础,编程语言]
---

# 1. 入门

`Python`语法采用缩进方式，以`#`开头的语句是注释，当语句以`:`为结尾时，缩进的语句视为代码块

`Python`是动态语言，其变量本身类型不固定。与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错，`Java`就属于静态语言

空值`None`是`Python`里一个特殊的值，`None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值

```python
a = -1;
if a>=0:
    print(a)
else:       #冒号下相同缩进部分视为在同一个代码块
    print(-a)
    print("hello")
print("你好")
```

# 2. 输入输出

```python
# 单引号引起来是字符串
print('Hello')		
# 逗号隔开输出不同内容，同时会自动填上一个空格
print('100+200=',100+200)		
# 若不想要空格可以在print最后添加参数sep=''，默认情况sep=' '
print('100+200=',100+200,sep='')

num = 15
# 也可以使用这种方式进行格式化输出
print(f"num是{num}")	
num = 3.141592653589793
# 保留3位小数
print('{:.3f}'.format(num))
```

```python
# input()是输入函数，输入结果放入name变量，python不需要手动定义类型
name = input();	
# 在提示语句后输入
name = input('请输入：');	
```

# 3. 数据类型和变量

> 计算机能处理的远不止数值，还可以处理文本、图形、音频、视频、网页等各种各样的数据,不同的数据，需要定义不同的数据类型。

## 3.1 整数

1. `Python`可以处理**任意大小的整数**，当然包括负整数，在程序中的表示方法和数学上的写法一模一样，例如：`1`，`100`，`-8080`，`0`
2. 计算机由于使用二进制，所以，有时候用十六进制表示整数比较方便，十六进制用`0x`前缀和`0-9`，`a-f`表示，例如：`0xff00`，`0xa5b4c3d2`
3. 对于很大的数，例如`10000000000`，很难数清楚0的个数。`Python`允许在数字中间以`_`分隔，因此，**写成`10_000_000_000`和`10000000000`是完全一样的**。十六进制数也可以写成`0xa1b2_c3d4`

## 3.2 浮点数

> 浮点数也就是小数，之所以称为浮点数，是因为按照科学记数法表示时，一个浮点数的小数点位置是可变的
>
> 比如， $$1.23\times 10^9$$ 和 $12.3\times 10^8$ 是完全相等的。浮点数可以用数学写法，如`1.23`，`3.14`，`-9.01`

+ 对于很大或很小的浮点数，必须用科学计数法表示，把`10`用`e`替代，$1.23*10^9$ 就是`1.23e9`，或者`12.3e8`，`0.000012`可以写成`1.2e-5`
+ 整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的，而浮点数运算则可能会有四舍五入的误差【详情请看计算机组成原理】

## 3.3 字符串

> 字符串是以单引号`'`或双引号`"`括起来的任意文本，比如`'abc'`，`"xyz"`

+ 如果字符串内部既包含`单引号''`又包含`双引号""`怎么办？可以用转义字符`\`来标识，使特殊字符失去特殊含义

  ```python
  s = 'I\'m \"OK\"!'
  # 输出：I'm "OK"!
  print(s)
  ```

+ 转义字符`\`除了使特殊字符失去特殊含义，也能让部分字符得到特殊含义，比如`\n`表示换行，`\t`表示制表符，字符`\`本身也要转义，所以`\\`表示的字符就是`\`

+ 如果字符串里面有很多字符都需要转义，就需要加很多`\`，为了简化，Python还允许用`r''`表示`''`内部的字符串默认不转义

  ```python
  # 输出\t
  print(r'\t')	
  ```

+ 如果字符串内部有很多换行，用`\n`写在一行里不好阅读，为了简化，`Python`允许用`'''...'''`的格式表示多行内容

  ```python
  # 输出 line1 line2 line3三行
  print('''line1
  line2
  line3''')
  ```

## 3.4 布尔值

> 布尔值和布尔代数的表示完全一致，一个布尔值只有`True`、`False`两种值，要么是`True`，要么是`False`，在`Python`中，可以直接用`True`、`False`表示布尔值，也可以通过布尔运算计算出来

+ 布尔值可以用`and【与】`、`or【或】`和`not【非】`计算

  ```python
  # 输出False
  print(True and False)		
  # 输出True
  print(not False)		
  # 输出False
  print(17 > 18)				
  ```

## 3.5 常量

常量就是不能变的变量，比如常用的数学常数`π`就是一个常量。在`Python`中，通常用**全部大写的变量名表示常量**

```python
PI = 3.14159265359
```

但事实上`PI`仍然是一个变量，`Python`根本没有任何机制保证`PI`不会被改变，所以，用**全部大写的变量名表示常量只是一个习惯上的用法**

+ `/`除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数
+ 还有一种除法是`//`，称为地板除，结果只保留整数部分

```python
# 输出为 3.0
print(9 / 3)	
# 输出为 3
print(10 // 3)	
```

## 3.6 理解变量在内存中的表示

```python
a = 'ABC'
b = a
a = 'XYZ'
# 最终输出ABC
print(b)		
```

+ 当`a`赋给`b`时，实际上是**令`b`指向了`a`此时指向的常量数据`ABC`**
+ 由于`b`本质指向的是数据，因此随后`a`指向了新数据后并不会影响`b`的指向
+ 因此`b`最终指向的依然是`ABC`

# 4. 字符串

> `Python`的字符串类型是`str`，在内存中以`Unicode`表示，一个字符对应若干个字节。如果要在网络上传输，或者保存到磁盘上，就需要把`str`变为以字节为单位的`bytes`

## 4.1 编码

在操作字符串时，我们经常遇到`str`和`bytes`的互相转换。为了避免乱码问题，应当始终坚持使用`UTF-8`编码对`str`和`bytes`进行转换。

+ `ord(ch)/chr(num)`：获取字符的 Unicode 编码/将 Unicode 编码转换为字符

  > 在英文字符中，Unicode 编码与 ASKII 码一致

+ `encode(coding_str)/decode(coding_str)`：将字符串按照指定格式编码/解码

  > Python 对 bytes 类型的数据用带 b 前缀的单引号或双引号表示

  ```python
  # 此时x存放的是bytes类型，编码方式为ascii
  x=b'ABC'
  # 将字符串转换为UTF-8码对应的bytes存储
  print('中文'.encode('UTF-8'))
  # 以ASCII码的方式解释字节流，输出ABC
  print(b'ABC'.decode('ASCII'))	
  ```

## 4.2 其他方法

+ `lstrip()/rstrip()/strip()`：删除字符串前/后/所有空格

+ `lower()/upper()/capitalize()/title()`：将字符串全部转为小/大写/首字母大写/每个连续字母首字母大写

+ `swapcase()`：把字符串中的大写字母转换成小写，小写字母转换成大写

+ `split(sep)`：使用指定的分隔符 sep（默认为空格）将字符串拆分为列表

+ `startswith(prefix)/endswith(suffix)`: 检查字符串是否以指定的前缀开头/后缀结尾

+ `replace(old, new, count)`：用新字符串替换旧字符串，如果指定了count，则替换不超过 count 次

+ `find(sub)`: 查找子串 sub 在字符串中第一次出现的位置，若找不到返回 -1

+ `count(sub)`：返回子串 sub 在字符串中出现的次数

+ `center(width, fillchar=' ')`：用于将字符串居中对齐，并使用指定的填充字符（默认为空格）填充空白部分，使得字符串长度达到指定的宽度 width

  ```python
  s = "hello"
  centered_string = s.center(10, '*')
  print(centered_string)  # 输出：**hello***
  ```

+ `isdigit()`：检查字符串是否只包含数字字符

+ `str.join(iterable)`：将可迭代对象中的元素以指定字符串连接起来

  ```python
  l = ['1','2','3']
  # 输出为：1---2---3
  print('---'.join(l))
  ```

## 4.3 格式化

`%`运算符用来格式化字符串。在字符串内部，`%s`表示用字符串替换，`%d`表示用整数替换，有几个`%?`占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个`%?`，括号可以省略

如果不太确定应该用什么，`%s`永远起作用，它会把任何数据类型转换为字符串

字符串里面的`%`是一个普通字符时需要转义，用`%%`来表示一个`%`

```python
# 输出'Hello, world'
print('Hello, %s' % 'world')	
# 输出'Hi, Michael, you have $1000000.'
print('Hi, %s, you have $%d.' % ('Michael', 1000000))
```

# 5. 数据结构

## 5.1 链表 list

Python 内置的一种数据类型是 list ，可以随时添加和删除其中的元素，将其理解为链表即可，同时 Python 中的 list 更加强大，**内部可以存放不同类型的元素**

```python
# 创建一个list
p = ['asp', 'php']
# 此时s[2][1]等价于p[1]
s = ['python', 'java', p, 'scheme']	
# 某个元素在不在列表中，返回布尔型
ele in/not in list_name
```

### list方法

+ `append(x)`：在列表末尾添加一个元素 x

+ `extend(iterable)`：将可迭代对象中的元素添加到列表的末尾

+ `insert(i,x)`：在指定的位置 i 插入元素 x

+ `remove(x)`：移除列表中第一个值为 x 的元素。

+ `pop([i])`：移除列表中指定位置的元素，并返回该元素

  > 默认移除并返回最后一个元素

+ `clear()`：移除列表中的所有元素

+ `index(x,start,end)`：返回列表中第一个值为 x 的元素的索引。可指定搜索的起始和结束位置

+ `count(x)`：返回列表中值为 x 的元素的个数

+ `sort(key,reverse)`：对列表进行排序

  > 默认为升序排序，配合 reverse=True 可完成降序，通过指定排序的键函数 key 可以实现自定义规则再排序

  ```python
  lis = ['apple','lemon','pear','peach']
  def fn(x):
      return x[::-1]
  # 此时自定义规则fn，会使得lis每个元素逆序后再排序，如apple变为elppa
  # reverse=True则是在fn规则下按照字符串降序进行排序
  lis.sort(key=fn,reverse=True)
  # 虽然排序元素有所变化，但不会影响lis本身，即改变了每个元素的最终索引位置
  # 最后排序结果为：['pear', 'lemon', 'peach', 'apple']
  print(lis)
  ```

+ `reverse()`：反转列表中的元素顺序

+ `copy()`：返回列表的浅拷贝

## 5.2 静态链表 tuple

> tuple 一旦初始化就不能修改【所谓“不变”是说每个元素指向永远不变】，这是其与`list`的唯一区别

```python
# tuple在定义时，元素必须被确定下来
t = (1, 2)
# 只有1个元素的tuple定义时必须加一个逗号，否则会与数学中的小括号歧义
t = (1,)
```

```python
# tuple和list具有拆包特性
seq=(1,2,3,4)		
# 将1赋值给a，将2赋值给b，剩余部分成为列表放入rest
a,b,*rest=seq	
```

## 5.3 字典 dict (map)

**创建方式**

> 创建 {'one': 1,'two': 2,'three': 3} 对应的字典有如下几种创建方式

```python
# 传入关键字
d = dict(one=1, two=2, three=3)
# 映射函数方式来构造字典
d = dict(zip(['one', 'two', 'three'], [1, 2, 3])) 
# 可迭代对象方式来构造字典
d = dict([('one', 1), ('two', 2), ('three', 3)])
# 使用{key:value}
d = {'one': 1,'two': 2,'three': 3} 
```

### dict方法

+ `get(key,default=None)`：返回字典中键 key 对应的值，如果键不存在则返回默认值 default

  也可以直接使用 key 作为索引取出 value 值或者进行添加键值对，类似数组

  ```python
  # 取出键值对应的值
  d['Michael']
  # 放入键值对
  d['Adam'] = 67  
  ```

+ `pop(key,default)`：移除并返回字典中键 key 对应的值，如果键不存在则返回默认值 default

  > 未填写 default 且 key 不存在时会报错，此时需要根据 "`key in d`" 返回值进行判断 key 是否存在

+ `clear()`：移除字典中的所有元素

+ `copy()`：返回字典的一个副本

+ `items()`: 返回一个包含所有键值对的元组的列表（用于 for 循环遍历）

+ `keys()/values()`: 返回字典中所有的键/值

+ `setdefault(key, default)`：返回字典中键 `key` 对应的值，如果键不存在则插入键值对 key 和默认值 default（默认为 None），并返回 default

+ `update(other)`：更新字典中的键值对，可以接受另一个字典或者键值对序列作为参数

  ```python
  # 这两种方式都能成功添加键值对
  d.update([('four', 4)])
  d.update({'four': 4})
  ```

## 5.4 集合 set

> `set`的原理与`dict`一致，不过其只存储`key`值

```python
# 得到一个空集合
s=set()				
# set的赋值初始化需要list作为输入集合
s=set([1, 2, 3])	
```

### set方法

+ `add(ele)`：向集合中添加一个元素

+ `update(*others)/union(*others)`：得到多个集合的并集

  > 二者类似，前者修改原集合上更新，后者返回合并值

  ```python
  # 将set2，set3与set1的差集添加进set1中
  set1.update(set2,set3)
  # 得到并集
  s1 | s2	
  ```

+ `remove(ele)/discard(ele)`：移除集合中指定的元素，如果元素不存在，会引发 KeyError 错误/不报错

+ `pop()`：移除并返回集合中的一个任意元素，如果集合为空则引发 KeyError 错误

+ `clear()`：移除集合中的所有元素

+ `copy()`：返回集合的浅拷贝

+ `intersection(*others)`：返回当前集合与其他集合的交集

  > 交集即公共部分

  ```python
  # 得到s1与s2的交集
  s1 & s2	
  ```

+ `difference(*others)`：返回当前集合与其他一个或多个集合的差集

  > 差集即除去公共部分

+ `symmetric_difference(other)`：返回当前集合与另一个集合的对称差集

  > 对称差集指两个集合中独有元素组成的集合，如 {1,2,3} 与 {2,3,4} 的对称差集为 {1,4}

+ `issubset(other)`：判断当前集合是否为另一个集合的子集

+ `isdisjoint(other)`：判断当前集合与另一个集合是否没有交集

+ `issuperset(other)`: 判断当前集合是否为另一个集合的超集

  > 如果一个集合包含另一个集合的所有元素，那么这个集合被称为另一个集合的超集

# 6. 基础用法

## 6.1 条件判断

`Python`的条件判断是不需要小括号括起来的，同时由于其依靠相同缩进代表同一代码块，因此也无需中括号

```python
age = 3
#只要age是非零数值、非空字符串、非空list等，就判断为True，否则为False
if age >= 18:			#不要少写冒号
    print('adult')
elif age >= 6:			#else if在此处缩写为elif
    print('teenager')
else:
    print('kid')
```

## 6.2 循环

```python
# 定义一个list
nums=[1,2,3]	
# for的用法，不要忘了冒号:，while循环同样需要冒号
for x in nums:	
    # x是nums中的元素
    print(x)

# Python内置的enumerate函数可以把一个list变成[索引-元素]对，这样就可以在for循环中同时迭代索引和元素本身
for i, value in enumerate(['A', 'B', 'C']):
    # i即此元素在list中的下标
    print(i, value)		
    
# 也可以同时引用多个变量，此时就像是有多个索引进行遍历 
for x, y in [(1, 1), (2, 4), (3, 9)]:
    print(x, y)  
```

## 6.3 赋值

```python
# 后边的部分先得到，接着按顺序赋值
a, b = b, a + b
# 上述赋值语句等价于👇
# t是一个tuple
t = (b, a + b) 
a = t[0]
b = t[1]
```

## 6.4 与and或or非not

`and`和`or`返回第一个能让其确定结果为`True`或者`False`的值

```python
# 输出None，因为None视为False，此时已经可以指定整体输出为False
print(None and 'a')		
# 输出None，因为a不为None视为True，此时结果看后半部分，即None
print('a' and None)		
# 输出b，理由同上
print('a' and 'b')		
# 空字符串也被视为False
# 输出a，因为a不为None视为False，此时整体已可以得知为False
print('a' or 'b')		
```

## 6.5 函数

在`Python`中，定义一个函数要使用关键词`def`，函数名，括号，括号中的参数以及冒号

在缩进块中编写函数体，函数的返回值用`return`语句返回，如果没有`return`语句，函数执行完毕后也会返回结果，只是结果为`None`，`return None`可以简写为`return`

```python
num=0
def my_abs(x):
    # num是全局变量，想在函数中使用需要用global关键字标记
    global num	
    if x >= 0:
        return x
    else:
        return num
```

### 6.5.1 空函数

如果想定义一个什么事也不做的空函数，可以用`pass`语句：

```python
def nop():
    pass
```

`pass`语句什么都不做，那有什么用？实际上`pass`可以用来作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个`pass`，让代码能运行起来。`pass`还可以用在其他语句里，如下：

```python
if age >= 18:
    # 此处若缺少了pass，代码运行就会有语法错误
    pass		
```

### 6.5.2 返回值

> `Python`可以允许返回多个值，其原理是返回一个`tuple`，多个变量可以同时接收一个`tuple`，**按位置赋给对应的值**

```python
def move(x, y):
    nx = x 
    ny = y 
    # 返回多个值
    return nx, ny	
# x,y依次接受move函数返回的nx,ny
x, y = move(100, 100)		
```

### 6.5.3 默认参数

> 当函数有多个参数时可以指定默认参数，不过需要保证**必选参数在前，默认参数在后**

```python
# 使用时当n处不填参数也不会报错，n默认为2
def power(x, n=2):	
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

有多个默认参数时，调用的时候，既可以按顺序提供默认参数，也可以不按顺序提供部分默认参数。

当不按顺序提供部分默认参数时，需要把参数名写上。比如调用`enroll('Adam', 'M', city='Tianjin')`，意思是，`city`参数用传进去的值，其他默认参数继续使用默认值

**不过使用时需要注意一个陷阱：默认参数必须指向不变对象！**

```python
# Python函数在定义的时候，默认参数L的值就被计算出来了
# 由于L的默认参数指向的是地址，因此下次使用时会保留上次添加的数据
def add_end(L=[]):
    L.append('END')
    return L

# 修改如下
# 由于None是一个不变对象，因此保证每次L指向的内容不发生改变
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
```

### 6.5.4 可变参数

> 可变参数就是传入的参数个数是可变的，可以是`1`个、`2`个到任意个，还可以是`0`个

定义可变参数和定义一个`list`或`tuple`参数相比，仅仅在参数前面加了一个`*`号。在函数内部，**参数`numbers`接收到的是一个`tuple`**，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括`0`个参数

```python
def calc(*numbers):
    sum=0
    # 将传入的参数全部进行相加
    for n in numbers:
        sum+=n
    return sum
# 得到2，3，4之和
result = calc(2,3,4)

# 也可以在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去
nums = [1,2,3]
calc(*nums)
```

### 6.5.5 关键字参数

> 关键字参数允许传入`0`个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个`dict`

```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
# kw接收键值对
person('Adam', 45, gender='M', job='Engineer')
# 也可以直接传入定义好的dict
extra = {'city': 'Beijing', 'job': 'Engineer'}
person('Jack', 24, **extra)
```

**`kw`获得的`dict`是`extra`的一份拷贝，对`kw`的改动不会影响到函数外的`extra`**

#### 命名关键字参数

> 当使用了关键字参数时，正常情况下会将所有输入的键值对都进行接收，若我们**只想接收指定关键值则需要用到命名关键字参数**

```python
# 命名关键字参数需要一个特殊分隔符*, *后面的参数被视为命名关键字参数
# 只接受city和job的关键字
def person(name, age, *, city, job):	
    print(name, age, city,job)
# 调用函数，其中key = value必须写全，不能单写一个value    
# 命名关键字参数可以有缺省值【即默认参数】，从而简化调用
person('Jack',24, city='Beijing', job='Engineer')
```

```python
# 如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了
# *args说明是可变参数
def person(name, age, *args, city, job):	
    print(name, age, args, city, job)
```

### 6.5.6 参数组合

> `Python`中参数定义的顺序必须是：**必选参数、默认参数、可变参数、命名关键字参数，关键字参数**

```python
# a和b对应必选参数，c为默认参数，*开始的位置表示命名关键字【只接收d和kw关键值】
def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
# 除了挨个传参外，也可以通过一个tuple和dict也调用上述函数，不过需要保证tuple内容可以与参数对上 
args = (1, 2, 3)
# kw中d的键值对会对应给d的参数，剩下的内容才会归入参数中的kw
kw = {'d': 88, 'x': '#'}
f2(*args, **kw)
```

**虽然可以组合多达`5`种参数，但不要同时使用太多的组合，否则函数接口的可理解性很差**

# 7. 基础内置函数

> 即`Python`已经封装好，我们可以直接拿来用的函数

## 7.1 数学

```python
# 得到绝对值
abs(-100)
# 得到最大值，参数可多个
max(2, 3, 1, -5)	
# 开平方
math.sqrt()	
# 对num进行四舍五入，保留2位小数
round(num,2)		
```

## 7.2 类型转换

```python
# 转为整型
int('123')			
# 转为浮点型，Python没有double
float('12.34')		
# 转为字符串
str(1.23)			
# 转为布尔型，非空即为True
bool(1)				
```

## 其他

+ `range(start,end,step)`：按照指定步长 step 生成 [start,end) 的整数列表，当 step 为负数时逆序读取

  > 当 start 大于end 时 step 要为负数，生成 (end,start] 的整数序列，同样对此序列逆序读取

  ```python
  # 输出：10，8，6，4，2
  for i in range(10, 1, -2):
      print(i)
  ```


## 7.3 数据类型检查isinstance

```python
# 数据类型检查可以用内置函数isinstance()实现
# 若x属于int型或float型时返回True
isinstance(x, (int, float))		
```

## 7.4 长度len

```python
# len()函数计算的是str的字符数【中文同适用】，如果换成bytes，len()函数就计算字节数
# 输出2，因为有两个字符
print(len('中文'))	
# 输出3，UTF-8编码一个中文字符通常占3个字节，所以输出6
print(len('你好'.encode('utf-8'))) 
```

# 8. 高级特性

## 8.1 切片

> `Python`提供一系列取指定索引范围的操作，形象地称为切片`Slice`，`list`，`tuple`，字符串都可进行切片操作

`a[x:y:z]`：**`x`表示切片起点，`y`表示切片终点【不包括】，`z`表示步长**。如果不指定`x`和`y`，则默认开始和最后；如果不指定`z`，则默认步长为`1`。当**`z`为`-1`时代表倒序且步长为`1`**，此时`x`与`y`对应的是倒序后的下标

```python
L=['tom','jack','jery']
# L[0:2]表示，从索引0开始取，直到索引1为止
L[0:2]
# 如果第一个索引是0，还可以省略，写成L[:2]
# L[-2:-1]取倒数第二个，但不包括倒数第一个
# L[-2:]从倒数第二个开始往后取至末尾

n='1234'
# -1代表逆序，此时起点是最后一个元素，由后往前取，输出[4,3,2,1]
n[::-1]	
# 从下标2开始往前取，所以是[3, 2, 1]
n[2::-1]	
# 从最后一个元素处取到下标2【不包括】，所以是[4]
n[:2:-1]	
```

## 8.2 迭代

> 如果给定一个`list`或`tuple`，我们可以通过`for`循环来遍历这个`list`或`tuple`，这种遍历我们称为迭代`Iteration`

在`Python`中，迭代是通过`for ... in`来完成的，默认情况下，`dict`迭代的是`key`。如果要迭代`value`，可以用`for value in d.values()`，**如果要同时迭代`key`和`value`，可以用`for k, v in d.items()`**

## 8.3 列表生成式

> 创建列表的时可以直接在列表内写表达式，符合表达式的部分成为列表元素

```python
# 如下，生成[4, 16, 36, 64, 100]
L = []
for x in range(1, 11):
    if x % 2 == 0:
        L.append(x*x)
        
# 也可以在表达式中直接这样写👇
# 写列表生成式时，把要生成的元素x * x放到前面，后面跟for循环，就可以把list创建出来
# 此时if后不能用else
L=[x * x for x in range(1, 11) if x % 2 == 0]	

# 当if语句放在for前时必须使用else，因为此时if..else是表达式而不是过滤条件
L=[x if x % 2 == 0 else -x for x in range(1, 11)]
# 此时L=[-1, 2, -3, 4, -5, 6, -7, 8, -9, 10]
```

## 8.4 生成器

> `Python`一边循环一边计算的机制，称为生成器：`generator`。当我们仅仅需要使用列表生成式中的少量元素时，使用`generator`可以节省根据需要创建我们需要的元素，从而节约空间

### 8.4.1 创建generator方法 1

> 把一个列表生成式的`[]`改成`()`，就创建了一个`generator`

```python
# 这里通过列表生成式得到list
l = [x * x for x in range(10)]	
# 这里得到generator
g = (x * x for x in range(10))	

# g内元素如果要一个一个打印出来，可以通过next()函数获得generator的下一个返回值
# 直到计算到最后一个元素，没有更多的元素时，抛出StopIteration的错误
# 不过此方法一般不用，因为通过循环同样可以对g进行遍历且不用担心异常
next(g)		
```

### 8.4.2 创建generator方法 2

> 如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个`generator`函数，调用一个`generator`函数将返回一个`generator`

`generator`的函数会在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行

**调用`generator`函数会创建一个`generator`对象，多次调用`generator`函数会创建多个相互独立的`generator`**

```python
def odd():
    print('hh')
    # yield是generator函数的标志
    yield 1		
    print('jj')
    yield(2)
    print('kk')
    yield(3)
    
# 调用该generator函数时，首先要生成一个generator对象，然后用next()函数不断获得下一个返回值  
# 得到一个generator对象
o = odd()	
# 输出hh，返回1
next(o)		
# 输出jj，返回2
next(o)		
# 输出kk，返回3
next(o)		
# 此时函数已经执行完毕，报错StopIteration
next(o)		
```

**实际上，遍历`generator`很少使用`next`方法，通常能用`for`解决就用`for`**

```python
# 斐波那契数列
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'

for n in fib(6):
    # 输出1,1,2,3,5,8
	print(n)	
# 但是此方法只能拿到yield返回值，无法拿到return返回值
g = fib(6)

# 若需要拿到return返回值则需要进行如下操作
while True:
	try:
		x = next(g)
		print('g:', x)
     # 发生异常后才会执行此代码块
	except StopIteration as e:	
         # e.value即return返回值
		print('Generator return value:', e.value) 
		break
```

## 8.5 迭代器

我们已经知道，可以直接作用于`for`循环的数据类型有以下几种：

+ 一类是集合数据类型，如`list`、`tuple`、`dict`、`set`、`str`等；
+ 一类是`generator`，包括生成器和带`yield`的`generator function`

这些可以直接作用于`for`循环的对象统称为可迭代对象：`Iterable`，可以使用`isinstance()`判断一个对象是否是`Iterable`对象

**可以被`next()`函数调用并不断返回下一个值的对象称为迭代器**：`Iterator`

生成器都是`Iterator`对象，但`list`、`dict`、`str`虽然是`Iterable`，却不是`Iterator`

```python
# 通过iter()函数可以把Iterable变成Iterator
# 将list转变为Iterator对象后进行判断，返回True
isinstance(iter([]), Iterator)	
```

**为什么`list`、`dict`、`str`等数据类型不是`Iterator`？**

> 因为`Python`的`Iterator`对象表示的是一个数据流，`Iterator`对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却**不能提前知道序列的长度**，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。`Iterator`甚至可以表示一个无限大的数据流，例如全体自然数。而使用`list`是永远不可能存储全体自然数的

+ 凡是可作用于`for`循环的对象都是`Iterable`类型
+ 凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列

# 9. 函数式编程

> 函数式编程是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，**任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用**。
>
> 而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，**同样的输入，可能得到不同的输出，因此，这种函数是有副作用的**。
>
> 函数式编程的一个特点是：**允许把函数本身作为参数传入另一个函数，还允许返回一个函数！**

## 9.1 高阶函数

> 变量可以指向函数，函数的参数能接收变量，**一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数**

函数名其实就是指向一个函数对象的引用，**完全可以把函数名赋给一个变量**，相当于给这个函数起了一个“别名”

```python
# 变量a指向abs函数
a = abs 
# 所以也可以通过a调用abs函数
a(-1) 	

# 将1赋给abs，此时abs不再指向绝对值函数，而指向数字1
abs=1;	
```

```python
# 一个简单例子
# f作为参数接受的是函数
def add(x,y,f):		
	return f(x) + f(y)
# 输出11
print(add(5,6,abs))		
```

### 9.1.1  map

`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回

```python
def f(x):
    return x * x
# list中的元素依次执行f(x)
r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])	
# 用list装Iterator
r=list(r)	
# 输出[1, 4, 9, 16, 25, 36, 49, 64, 81]
print(r)	
# 将list中的元素都变成字符串
l=list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```

### 9.1.2 reduce

`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算

```python
# 👇等价于 f(f(f(x1, x2), x3), x4)
reduce(f, [x1, x2, x3, x4]) 
# 如下简单例子，对序列求和
def add(x, y):
     return x + y
# 先 f(1,3)，再将结果放入问号 f(?,5)...
reduce(add, [1, 3, 5, 7, 9])	
```

```python
def fn(x, y):
	return x * 10 + y

def char2num(s):
	digits = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
	return digits[s]

# reduce与map结合使用，实现把字符串转整型
reduce(fn, map(char2num, '13579')) 
```

### 9.1.3 filter

与`map()`类似，`filter()`也接收一个函数和一个序列，不过和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素

```python
# 一个简单例子
def is_odd(n):
    # 结果为True的元素保留
    return n % 2 == 1	
# 保留下奇数
# 结果: [1, 5, 9, 15]
l=list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15])) 
```

```python
def not_empty(s):
    # 如果s不是None或空串说明为True，此时返回去除多余空格的字符串
    # 如果s是None或空串说明为False，此时配合filter对这部分数据不保留
    return s and s.strip()
# filter返回的是一个Iterator
l = list(filter(not_empty, ['A', '', 'B', None, 'C', '  ']))
# 结果: ['A', 'B', 'C']
```

### 9.1.4 sorted/sort

> `sorted()`函数也是一个高阶函数，它可以接收一个`key`函数来实现自定义的排序

```python
# 默认从小到大排序
sorted([36, 5, -12, 9, -21])	
# 加上参数就是从大到小排序
sorted([36, 5, -12, 9, -21], reverse=True)	
# key指定的函数将作用于list的每一个元素上，并根据key函数返回的结果进行排序，不过只能是内置函数
# 根据绝对值大小从小到大排序
sorted([36, 5, -12, 9, -21], key=abs)	

# 如果需要进行自定义函数排序则需要使用容器内部的sort方法进行操作
```

## 9.2 返回函数

> 高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回

```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    # 返回值是sum()这个函数
    return sum	

# 多次调用lazy_sum时返回的函数是独立的
# 此时得到的是sum()这个函数，并未进行求和
f = lazy_sum(1, 3, 5, 7, 9)	
# 调用f()函数时才开始执行
num=f()		
```

### 闭包

> 闭包`Closure`是一种程序结构，指内部函数`sum`可以**引用外部函数`lazy_sum`的参数和局部变量**，当`lazy_sum`返回函数`sum`时，相关参数和变量都保存在返回的函数中

```python
def count():
    fs = []
    for i in range(1, 4):
        def f():
             return i*i
        fs.append(f)
    return fs

# 此时f()并没有真正执行，但是当count()执行完毕并返回fs后，i已经变成了3
f1, f2, f3 = count()	
# 调用f1()等函数时f()才真正执行，此时返回 3 * 3 = 9
# 输出9，9，9
print(f1(),f2(),f3())	
```

**返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量【如上👆】**

**如果一定要引用循环变量怎么办？**

> 可以再创建一个函数，用该函数的参数绑定循环变量当前的值，无论该循环变量后续如何更改，已绑定到函数参数的值不变，缺点是代码较长，可利用`lambda`函数缩短代码【`9.3`学】
>
> ```python
> def count():
>  def f(j):
>      def g():
>          return j*j
>      return g
>     
>  fs = []
>  for i in range(1, 4):
>      # f(i)立刻被执行，因此i的当前值被传入f()
>      fs.append(f(i)) 
>  return fs
> 
> f1, f2, f3 = count()	
> # 此时输出1，4，9
> print(f1(),f2(),f3())	
> ```
>

#### nonlocal

使用闭包，就是内层函数引用了外层函数的局部变量，如果内层函数直接对外层变量赋值，由于`Python`解释器会把`x`当作函数`fn()`的局部变量，它会报错。原因是`x`作为局部变量并没有初始化，直接计算`x+1`是不行的，但我们其实是想**引用`inc()`函数内部的`x`，所以需要在`fn()`函数内部加一个`nonlocal x`的声明**

```python
def inc():
    x = 0
    def fn():	
        # nonlocal x		# 加此声明
        # 此时解释器会把x当作是外层变量
        x = x + 1			
        return x
    return fn

f = inc()
# 输出1
print(f()) 
# 输出2
print(f()) 
```

## 9.3 匿名函数

> 当我们在传入函数时，有些时候，不需要显式地定义函数，直接传入匿名函数更方便

关键字`lambda`表示匿名函数，冒号`:`前面的`x`表示函数参数。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数

```python
# 传入参数为x，返回值为x*x
f = lambda x: x * x	
# 输出 25
print(f(5))		
```

## 9.4 装饰器

> 在代码运行期间动态增加功能的方式，称之为“装饰器”`Decorator`，本质上，`decorator`就是一个返回函数的高阶函数

```python
def log(func):
    # 可以接受任何参数
    def wrapper(*args, **kw):	
        # 每个函数都有__name__属性，其封装了函数名
        print('call %s():' % func.__name__) 
        return func(*args, **kw)
    return wrapper

# 因为log是一个decorator，所以接受一个函数作为参数，并返回一个函数。我们要借助Python的@语法，把decorator置于函数的定义处
@log
def now():
    print('2015-3-25')
# 此时调用now()的输出如下：
call now():
2015-3-25
    
# 把@log放到now()函数的定义处，相当于执行了语句👇
# 相当于wrapper函数赋给now()，所以再次执行now()实际是wrapper，而原本的now早已通过参数func被wrapper获得，因此后续执行仍能定位到原now
now = log(now)	

# 不过由于当前now已经被赋为wrapper了，因此通过now._name_属性我们只能得到wrapper
# 因此我们工作仍未完成，需要把原始函数的__name__等属性复制到wrapper()函数中，否则，有些依赖函数签名的代码执行会出错
# 不需要编写wrapper.__name__ = func.__name__这样的代码，Python内置的functools.wraps就是干这个事的，所以，一个完整的decorator的写法如下
# 导入functools模块
import functools	
def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

```python
# 如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print('2015-3-25')
# 此时调用now()的输出如下：
execute now():
2015-3-25   
    
# 把@log放到now()函数的定义处，相当于执行了语句👇
# 前半部分返回decorator函数，接着传参now，后续执行如上
now = log('execute')(now) 
```

## 9.5 偏函数

> 通过设定参数的默认值，可以降低函数调用的难度，偏函数也可以做到这一点

```python
# int()默认将字符串当成十进制进行解析，我们通过设置参数默认值使得int2()将字符串当成二进制进行解析
def int2(x, base=2):
    return int(x, base)

# functools.partial就是帮助我们创建一个偏函数的，不需要我们自己定义int2()，可以直接使用下面的代码创建一个新的函数int2
import functools
int2 = functools.partial(int, base=2)
# 输出64
print(int2('1000000'))	
# 新的int2函数，仅仅是把base参数重新设定默认值为2，但也可以在函数调用时传入其他值
# 输出1000000
print(int2('1000000',base=10))	

# 创建偏函数时，实际上可以接收函数对象、*args和**kw这3个参数
# 上边创建的int2()，等价于👇	
kw = { 'base': 2 }
int('10010', **kw)

# 当传入*arg的参数时，实际上会被安排到最左边
max2 = functools.partial(max, 10)
max2(5, 6, 7)	
# 等价于👇
args = (10, 5, 6, 7)
max(*args)
```

# 10. 模块

> 为了编写可维护的代码，我们把很多函数分组，分别放到不同的文件里，这样，每个文件包含的代码就相对较少，很多编程语言都采用这种组织代码的方式。在`Python`中，一个`.py`文件就称之为一个模块`Module`
>
> 同时，为了避免模块名冲突，`Python`又引入了按目录来组织模块的方法，称为包`Package`。注意：**每一个包目录下面都会有一个`__init__.py`的文件，这个文件是必须存在的**，否则，`Python`就把这个目录当成普通目录，而不是一个包。`__init__.py`可以是空文件，也可以有`Python`代码，因为`__init__.py`本身就是一个模块

```python
# 此注释👇允许此.py文件直接在Unix/Linux/Mac上运行
#!/usr/bin/env python3	
# 此注释👇表示.py文件本身使用标准UTF-8编码 
# -*- coding: utf-8 -*-		

# 表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释
' a test module '		

# 使用__author__变量把作者写进去
__author__ = 'Michael Liao'	

# 上面部分👆是Python模块的标准文件模板
# 导入sys模块
import sys		

def test():
# sys模块有一个argv变量，用list存储了命令行的所有参数。argv至少有一个元素，因为第一个参数永远是该.py文件的名称
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')
# 当我们直接运行该模块文件时，Python解释器把一个特殊变量__name__置为__main__，而如果在其他地方导入该模块时，if判断将失败
if __name__=='__main__':
    test()
```

## 作用域

正常的函数和变量名是公开的`public`，在`Python`中，是通过`_`前缀来实现私有`private`

> 之所以我们说，`private`函数和变量“不应该”被直接引用，而不是“不能”被直接引用，是因为`Python`并没有一种方法可以完全限制访问`private`函数或变量，但是，从编程习惯上不应该引用`private`函数或变量

类似`__xxx__`这样的变量是特殊变量，可以被直接引用，但是有特殊用途，比如的`__author__`，`__name__`就是特殊变量，我们自己的变量一般不要用这种变量名

外部不需要引用的函数全部定义成`private`，只有外部需要引用的函数才定义为`public`

# 11. 面对对象编程

在`Python`中，所有数据类型都可以视为对象，当然也可以自定义对象。自定义的对象数据类型就是面向对象中的类`Class`的概念

## 11.1 类和实例

```python
# 定义一个类，object代表Student是从object继承下来的
class Student(object):	
    pass

# 创建Student实例
bart = Student()		
# 可以自由地给一个实例变量绑定属性，比如，给实例bart绑定一个name属性
bart.name = 'Bart Simpson'

# 由于类可以起到模板的作用，因此，可以在创建实例的时候，把一些我们认为必须绑定的属性强制填写进去
# 通过定义一个特殊的__init__方法【构造函数】，在创建实例的时候，就把name，score等属性绑上去
# __init__方法的第一个参数永远是self，表示创建的实例本身，因此，在__init__方法内部，就可以把各种属性绑定到self，因为self就指向创建的实例本身
class Student(object):
    def __init__(self, name, score):
        self.name = name
        self.score = score
        
# 有了__init__方法，在创建实例的时候，就不能传入空的参数了，必须传入与__init__方法匹配的参数，但self不需要传，Python解释器自己会把实例变量传进去        
bart = Student('Bart Simpson', 59)
```

## 11.2 访问限制

```python
# 如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线__，在Python中，实例的变量名如果以__开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问
class Student(object):
    def __init__(self, name, score):
        # 两个下划线，说明name是私有变量
        self.__name = name	
        self.__score = score
# 如果外部代码要获取或修改score怎么办？可以给Student类增加get和set这样的方法
    def get_score(self):
        return self.__score
    def set_score(self, score):
        self.__score = score
```

需要注意的是，在`Python`中，变量名类似`__xxx__`的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是`private`变量，所以，不能用`__name__`、`__score__`这样的变量名

## 11.3 继承和多态

当我们定义一个`class`的时候，可以从某个现有的`class`继承，新的`class`称为子类`Subclass`，而被继承的`class`称为基类、父类或超类`Base class`、`Super class`

```python
class Animal(object):
    def run(self):
        print('Animal is running...')
        
# Dog就是Animal的子类        
class Dog(Animal):
    pass
```

+ 子类可以获得父类的全部功能
+ 当子类和父类都存在相同的`run()`方法时，我们说，子类的`run()`覆盖了父类的`run()`，在代码运行的时候，总是会调用子类的`run()`。这样，我们就获得了继承的另一个好处：多态

对于静态语言（例如`Java`）来说，如果需要传入`Animal`类型，则传入的对象必须是`Animal`类型或者它的子类，否则，将无法调用`run()`方法

对于`Python`这样的动态语言来说，则不一定需要传入`Animal`类型。我们只需要保证传入的对象有一个`run()`方法就可以了。这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子

## 11.4 获取对象信息

判断对象类型，可以使用`type()`函数，它返回对应的`Class`类型。如果我们要在`if`语句中判断，就需要比较两个变量的`type`类型是否相同

```python
>>> type(abs)==types.BuiltinFunctionType
True
>>> type(lambda x: x)==types.LambdaType
True
>>> type((x for x in range(10)))==types.GeneratorType
True

# 但是对于class的继承关系来说，使用type()就很不方便，所以优先考虑isinstance()
>>> a = Animal()
>>> d = Dog()
>>> h = Husky()
# 子类属于父类，因此返回True
>>> isinstance(h, Animal)	
True
# 判断一个变量是否是某些类型中的一种
>>> isinstance([1, 2, 3], (list, tuple))	
True
# 基本类型也可以用isinstance()判断
>>> isinstance(b'a', bytes)	
True
```

```python
# 如果要获得一个对象的所有属性和方法，可以使用dir()函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法
>>> dir('ABC')
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']

# 类似__xxx__的属性和方法在Python中都是有特殊用途的，比如__len__方法返回长度。在Python中，如果你调用len()函数试图获取一个对象的长度，实际上，在len()函数内部，它自动去调用该对象的__len__()方法，所以，下面的代码是等价的
>>> len('ABC')
3
>>> 'ABC'.__len__()
3
# 我们自己写的类，如果也想用len(myObj)的话，就自己写一个__len__()方法
```

仅仅把属性和方法列出来是不够的，配合`getattr()`、`setattr()`以及`hasattr()`，我们可以直接操作一个对象的状态

## 11.5 实例对象和类属性

```python
# 如果类本身需要绑定一个属性，可以直接在class中定义属性，这种属性是类属性，归该类所有
>>> class Student(object):
...     name = 'Student'
...
# 创建实例s
>>> s = Student() 
# 打印name属性，因为实例并没有name属性，所以会继续查找class的name属性
>>> print(s.name) 
Student
# 打印类的name属性
>>> print(Student.name) 
Student

# 给实例绑定name属性
>>> s.name = 'Michael' 
# 由于实例属性优先级比类属性高，因此，它会屏蔽掉类的name属性
>>> print(s.name) 
Michael

# 但是类属性并未消失，用Student.name仍然可以访问
>>> print(Student.name) 
Student

# 如果删除实例的name属性
>>> del s.name 
# 再次调用s.name，由于实例的name属性没有找到，类的name属性就显示出来了
>>> print(s.name) 
Student
```

# 12. 面对对象高级编程

```python
from types import MethodType
# 定义一个函数作为实例方法
def set_age(self, age): 
    self.age = age
# 除了可以给实例对象绑定属性外，也可以给实例对象绑定方法
class Student(object):
    pass

s = Student()
 # 给实例绑定一个方法【需要借助MethodType】
s.set_age = MethodType(set_age, s)
# 调用实例方法
s.set_age(25) 
# 测试结果，输出 25
print(s.age) 

# 为了给所有实例都绑定方法，可以给 class 绑定方法
# 注意：给实例绑定方法和给类绑定方法写法是不一样的
Student.set_age = set_age
```

## 12.1 使用`__slots__`

> Python 允许在定义 class 的时候，定义一个特殊的`__slots__`变量，来限制该`class`实例能添加的属性

```python
class Student(object):
    # 用tuple定义允许绑定的属性名称
    __slots__ = ('name', 'age') 
    
# 创建新的实例
s = Student() 
# 由于'score'没有被放到__slots__中，所以不能绑定score属性，试图绑定score将得到AttributeError的错误
s.score = 99  
```

使用`__slots__`要注意，`__slots__`定义的属性仅对当前类实例起作用，对继承的子类是不起作用的

如果在子类中也定义`__slots__`，这样子类实例允许定义的属性就是自身的`__slots__`加上父类的`__slots__`

## 12.2 @property

> 利用`@property`可以实现通过属性调用【方法在此被视为属性】智能绑定`get`方法和`set`方法

```python
class Student(object):
    
	# @proerty绑定get方法，使得score属性拥有get方法
    @property	
    def score(self):
        return self._score

    # @score.setter使得set方法变为属性，使得score属性拥有set方法
    @score.setter	
    def score(self, value):
        if(value >= 60):
            self._score = value
        else:
            print('没有及格')

s=Student()
# score是方法名，但是被注释后成为属性名，赋值操作实则是调用set
s.score=61	
# 非赋值的调用实则调用get，因此输出 61
print(s.score)	
```

**特别注意：属性的方法名不要和实例变量重名，否则会造成无限递归！**

## 12.3 多重继承

> `Python`允许子类拥有多个父亲，通过多重继承，一个子类就可以同时获得多个父类的所有功能

```python
class Animal(object):
    def run(self):
        print('I am Animal')
class Runnable(object):
    def run(self):
        print('Running...')
        
# 同时有两个父类
class Dog(Runnable,Animal):	
d=Dog()
# 遇到同名方法时优先调用写在最左边的父类，输出Running...
d.run()		
```

### MixIn

> `MixIn`的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个`MixIn`的功能，而不是设计多层次的复杂的继承关系

```python
# 每个MixIn都有自己的功能
class RunnableMixIn(object):
    def run(self):
        print('Running...')
        
 # 我们根据需要将MixIn进行继承，从而获得想要的功能
class Dog(Animal,RunnableMixIn):
    pass
```

## 12.4 定制类

> 我们可以根据需要重写一些类方法从而使其能更好为我们服务

### 12.4.1 ` __str__`

> `__str__`类似与`Java`中的`toString`方法，通过重写此方法，可以使得直接输出类时按照我们想要的格式进行输出

```python
class Student(object):
	def __init__(self, name):
		self.name = name
    # 重写__str__
	def __str__(self):	
		return 'Student object (name: %s)' % self.name

# 输出 Student object (name: Michael)
print(Student('Michael')) 
```

### 12.4.2 `__iter__`

> 如果一个类想被用于`for ... in`循环，类似`list`或`tuple`那样，就必须实现一个`__iter__()`方法，该方法返回一个迭代对象，然后，`Python`的`for`循环就会不断调用该迭代对象的`__next__()`方法拿到循环的下一个值，直到遇到`StopIteration`错误时退出循环

```python
# 以斐波那契数列为例
class Fib(object):	
    def __init__(self):
        # 初始化两个计数器a，b
        self.a, self.b = 0, 1 

    # 使用for...in的时候会调用
    def __iter__(self):	
        return self # 实例本身就是迭代对象，故返回自己

    # for循环每进行一次就会调用一次
    def __next__(self):	
        # 计算下一个值
        self.a, self.b = self.b, self.a + self.b
        # 退出循环的条件
        if self.a > 100000: 
            raise StopIteration()
        # 斐波那契数列当前值
        return self.a 
 
for n in Fib():
    # 此时会打印Fib类中每次__next__的执行结果
	print(n)	
```

### 12.4.3 `__getitem__`

> 如果类想像`list`那样按照下标取出元素，需要实现`__getitem__()`方法

```python
class Fib(object):
    # n为下标值
    def __getitem__(self, n):
        a, b = 1, 1
        for x in range(n):
            a, b = b, a + b
        return a
    
f = Fib()
# 此时调用__getitem__方法，n为0，输出 1
print(f[0])	

# list还可以使用切片的方式访问，因此若我们将n定死为整型则无法使用切片访问
class Fib(object):
    def __getitem__(self, n):
        # n是索引
        if isinstance(n, int): 
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a
        # n是切片
        if isinstance(n, slice): 
            # 开始索引
            start = n.start	
            # 结束索引，但不包括此索引
            stop = n.stop	
            if start is None:
                start = 0
            a, b = 1, 1
            L = []
            for x in range(stop):
                # 过滤掉不需要的部分
                if x >= start:	
                    L.append(a)	
                a, b = b, a + b
            return L
        
f = Fib()
# 输出[1, 1, 2, 3, 5]
print(f[0:5])			
# 像上述👆这样设计仍然有缺陷，比如步数step还未处理等
```

与之对应的是`__setitem__()`方法，把对象视作`list`或`dict`来对集合赋值。最后，还有一个`__delitem__()`方法，用于删除某个元素。总之，通过上面的方法，我们自己定义的类表现得和`Python`自带的`list、tuple、dict`没什么区别，这完全归功于动态语言的“鸭子类型”，不需要强制继承某个接口。

### 12.4.4 `__getattr__`

> 正常情况下，当我们调用类的方法或属性时，如果不存在，就会报错。要避免这个错误，除了可以加上这个属性外，`Python`还有另一个机制，那就是写一个`__getattr__()`方法，动态返回一个属性

```python
class Student(object):
# 只有在没有找到属性的情况下才调用__getattr__，已有的属性不会在__getattr__中查找
    def __getattr__(self, attr):
        # 如果访问的属性是score就返回 99
        if attr=='score':	
            return 99
```

### 12.4.5 `__call__`

> 一个对象实例可以有自己的属性和方法，当我们调用实例方法时，我们用`instance.method()`来调用，同时任何类只需要定义一个`__call__()`方法，就可以直接对实例进行调用

```python
class Student(object):
    # 构造函数
    def __init__(self, name):	
        self.name = name

    def __call__(self):
        print('My name is %s.' % self.name)
        
s = Student('Michael')
# self参数不要传入，输出 My name is Michael.
s() 
```

`__call__()`还可以定义参数。对实例进行直接调用就好比对一个函数进行调用一样，所以你完全可以把对象看成函数，把函数看成对象。如果把对象看成函数，那么函数本身其实也可以在运行期动态创建出来，因为类的实例都是运行期创建出来的，这么一来，我们就模糊了对象和函数的界限。

那么，**怎么判断一个变量是对象还是函数呢？**

其实，更多的时候，我们需要判断一个对象是否能被调用，能被调用的对象就是一个`Callable`对象，比如函数和我们上面定义的带有`__call__()`的类实例

```python
# max函数可以被调用，所以为True
>>> callable(max)	
True
# list不可以被调用，所以为False
>>> callable([1, 2, 3])		
False
```

## 12.5 枚举类

> 当我们需要定义常量时，一个办法是用大写变量通过整数来定义，好处是简单，缺点是类型是`int`，并且仍然是变量。更好的方法是为这样的枚举类型定义一个`class`类型，然后，每个常量都是`class`的一个唯一实例。`Python`提供了`Enum`类来实现这个功能

```python
from enum import Enum

Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
for name,member in Month.__members__.items():
    # value 属性则是自动赋给成员的int常量，默认从1开始计数
    print(name,'=>',member,'=>',member.value)
# 输出格式类似于：Jan => Month.Jan => 1

# @unique 装饰器可以帮助我们检查保证没有重复值
@unique
class Weekday(Enum):
    # Sun的value被设定为0
    Sun = 0     
    Mon = 1
    Tue = 2
    Wed = 3
    Thu = 4
    Fri = 5
    Sat = 6
# 访问这些枚举类型可以有若干种方法
>>> print(Weekday.Tue)
Weekday.Tue
>>> print(Weekday['Tue'])	
Weekday.Tue
>>> print(Weekday.Tue.value)
2
>>> print(Weekday(1))
Weekday.Mon
```

## 12.6 使用元类

> 动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的

```python
class Hello(object):
    def hello(self, name='world'):
        print('Hello, %s.' % name)
        
h = Hello()
h.hello()
```

当`Python`解释器载入`hello`模块时，才会依次执行该模块的所有语句，执行结果就是动态创建出一个`Hello`的`class`对象

### type()

> `type()`函数可以查看一个类型或变量的类型，**`Hello`是一个`class`，它的类型就是`type`**，而`h`是一个实例，它的类型就是`class Hello`。
>
> 我们说`class`的定义是运行时动态创建的，而创建`class`的方法就是使用`type()`函数

`type()`函数既可以返回一个对象的类型【比如整数是`int`，字符串是`str`，`class`是`type`】，又可以创建出新的类型【不通过`class`定义而直接通过`class`定义的本质`type()`函数创建】

```python
# 先定义函数
def fn(self, name='world'): 
	print('Hello, %s.' % name)

# 要创建一个class对象，type()函数依次传入3个参数：
# 1. class的名称；
# 2. 继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；
# 3. class的方法名称与函数绑定，这里我们把函数fn绑定到方法名hello上
# 创建 class Hello
Hello = type('Hello', (object,), dict(hello=fn)) 
```

通过`type()`函数创建的类和直接写`class`是完全一样的，因为`Python`解释器遇到`class`定义时，仅仅是扫描一下`class`定义的语法，然后调用`type()`函数创建出`class`

正常情况下，我们都用`class Xxx...`来定义类，但是，`type()`函数也允许我们动态创建出类来，也就是说，**动态语言本身支持运行期动态创建类**，这和静态语言有非常大的不同，要在静态语言运行期创建类，必须构造源代码字符串再调用编译器，或者借助一些工具生成字节码实现，本质上都是动态编译，会非常复杂。

除了使用`type()`动态创建类以外，要**控制类的创建行为【得到类的实例】，需要使用`metaclass`**

### metaclass

> `metaclass`直译为元类，简单的解释就是：当我们定义了类以后，就可以**根据这个类创建出实例**，所以：先定义类，然后创建实例。可以把类看成是`metaclass`创建出来的“实例”

**正常情况下不会碰到需要使用`metaclass`的情况，等需要的时候再回来看这部分知识点！**

# 13. 文件

在 Python 使用 open 函数，可以打开一个已经存在的文件，或者创建一个新文件

+ `open(file, mode, buffering, encoding, errors, newline, closefd, opener)`

  + file：要打开的文件的路径或文件名

  + mode：文件打开模式，默认为 'r'（只读模式）

    > r（只读），w（可写），a（追加）
    > rb（二进制读），wb（二进制写），ab（二进制追加）

  + buffering：控制文件的缓冲策略

    > 0 表示关闭缓冲，1 表示行缓冲，-1 或任何大于 1 的正整数表示使用系统默认缓冲区大小

  + encoding：用于指定文件的编码格式，例如 'utf-8'、'gbk' 等

  + errors：指定编码错误处理方案，常用的包括 'strict'、'ignore'、'replace' 等

  + newline：控制换行符的处理。如果未指定，则使用系统默认的换行符

  + closefd：控制文件描述符的关闭行为

  + opener：用于自定义打开文件的函数

  ```python
  # 打开文件进行读取
  with open('example.txt', 'r') as f:
      content = f.read()
      print(content)
  # 打开文件进行写入
  # 若使用此语句打开文件，则文件流最后会自动关闭，不需要手动close()
  with open('example.txt', 'w') as f:
      f.write('Hello, world!')
  	# 刷新后写入的内容才会从缓存中发送到硬盘上
  	f.flush()
  ```

+ f.read()：读取文件所有内容，填写参数时则读取参数长度字节内容，返回str类型

+ f.readlines()：读取所有内容，每一行为 list 中的一个元素（换行符也会被读取）

+ f.readline()：调用一次读取一行内容

# 14. 异常处理

当我们认为某些代码可能会出错时，就可以用`try`来运行这段代码，如果执行出错，则后续代码不会继续执行，而是直接跳转至错误处理代码，即`except`语句块，执行完`except`后，如果有`finally`语句块，则执行`finally`语句块，至此，执行完毕

```python
try:
    print('try...')
    r = 10 / 0
    print('result:', r)
# python所有的错误类型都继承自BaseException，捕获错误时也会把子类一网打尽
# except: 是捕获所有异常
# 可以有多个except捕获不同的错误
except ZeroDivisionError as e:		
# except (xxx,xxx) as e	是捕获多个异常
    print('except:', e)
# finally是一定会执行的部分
finally:		
    print('finally...')
print('END')
```

**各异常的层级关系**👇

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202207291511213.png" alt="image-20220729151135974" style="zoom: 50%;" />

使用`try...except`捕获错误还有一个巨大的好处，就是可以跨越多层调用，比如函数`main()`调用`bar()`，`bar()`调用`foo()`，结果`foo()`出现异常了，此时`foo()`的异常会抛给`bar()`，`bar()`再抛给`main()`，只要`main()`捕获到了，就可以处理。换句话说，**异常是可以向上传递的**。

```python
def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2

def main():
    try:
        bar('0')
    except Exception as e:
        print('Error:', e)
    finally:
        print('finally...')
```

`Python`内置的`logging`模块可以非常容易地记录错误信息，通过配置，`logging`还可以把错误记录到日志文件里，方便事后排查。

如果错误没有被捕获，它就会一直往上抛，最后被`Python`解释器捕获，打印一个错误信息

**有时捕获错误目的只是记录一下，便于后续追踪**。但是，由于当前函数不知道应该怎么处理该错误，所以，最恰当的方式是继续往上抛，让顶层调用者去处理。好比一个员工处理不了一个问题时，就把问题抛给他的老板

```python
def foo(s):
    n = int(s)
    if n==0:
        # raise的作用是显式的抛出异常
        raise ValueError('invalid value: %s' % s)	
    return 10 / n

def bar():
    try:
        foo('0')
    except ValueError as e:
        print('ValueError!')
        # 主动抛出此异常，说明ValueError这个异常还未被解决，上层依旧可以进行捕获
        raise	
bar()
```

# 15. 模块

`Python`模块`Module`，是一个`Python` 文件，以`.py` 结尾。**模块能定义函数，类和变量，模块里也能包含**
**可执行的代码**。

`python`中有很多各种不同的模块，**每一个模块都可以帮助我们快速的实现一些功能**，比如实现和时间相关的功能可以使用`time`模块等。**我们可以认为一个模块就是一个工具包，每一个工具包中都有各种不同的工具供我们使用进而实现各种不同的功能**。

```python
# 模块在使用前需要先导入，导入语法如下👇
[from 模块名] import [模块│类│变量│函数│*] [as 别名]

# 常见的有以下👇几种形式
# 整个模块引入
import 模块名		
# 引入具体的方法
from 模块名 import 类、变量、方法等	
from 模块名 import *
import 模块名 as 别名
from 模块名 import 功能名 as 别名

# 导入time模块
import time	
# 通过模块调用内部方法
time.sleep(5)	
# 导入time.sleep()方法
from time import sleep	
# 直接使用方法名就可以完成调用
sleep(5)		
```

## 15.1 自定义模块

> 简单来说就是将自己写的模块导入当前正在处理的`.py`文件，类似于`Java`在一个类中使用另一个文件的类时也需要先导入此类

```python
# 当遇到不同模块的重名方法时，后导入的方法会覆盖先导入的方法
from module1 import test
from module2 import test
# 此时调用的是module2中的方法
test()		
```

在导入模块时，其实`Python`是将整个模块执行了一遍，而有时模块中有部分测试数据我们不希望他在被调用时执行则需要做以下处理👇

```python
def test(a, b):
    return a + b
# 只有发起运行的.py文件中的内置变量__name__才是__main__
# 因此当此模块被导入时，if下方的语句并不会执行
if __name__ == '__main__': 
    test(1 + 2)
```

`__all__`变量可以控制`import *`时哪些功能可以被导入

```python
# 此时若有程序通过import *导入此模块的方法时只能导到test1
__all__=['test1']		
def test1(a, b):
    return a - b
def test2(a, b):
    return a + b
```

## 15.2 自定义Python包

> 从物理上看，包就是一个文件夹，在该文件夹下包含了一个`_init_.py`文件，该文件夹可用于包含多个模块文件。从逻辑上看，包的本质依然是模块【**区别在于是否有`_init_.py`文件**】

在`PyCharm`中直接可以创建`Python Package`，创建后会自动生成`_init_.py`文件，接着我们将自己定义的模块放入此包下就行

```python
# 导入包的方法
# 导入my_package下的module1模块
from my_package import module1	
# 导入my_package下的module1，module2模块
from my_package import module1,module2	
# 导入my_package下的module模块的test()方法
from my_package.module1 import test	

# 还可以通过在__init__.py中设置__all__变量来控制import *的行为
# 例如在__init__.py写入此语句
__all__=['module2']		
# 此时只能导入module2模块
from my_package import *	
```

# 16. 引入第三方包

在`Python`程序的生态中，有许多非常多的第三方包【非`Python`官方】，可以极大的帮助我们提高开发效率，如：

+ 科学计算中常用的：`numpy`包
+ 数据分析中常用的：`pandas`包
+ 大数据计算中常用的：`pyspark，apache-flink`包
+ 图形可视化常用的：`matplotlib，pyecharts`

但是由于是第三方，所以`Python`没有内置，所以我们需要安装它们才可以导入使用

```python
# 第三方包的安装非常简单，我们只需要使用Python内置的pip程序即可
# 此语句在命令提示符中运行
pip install 包名称		
# 由于pip命令默认是从国外服务器下载包，因此速度较慢，我们可以为其设置镜像下载地址 
pip install deeplake -i https://pypi.tuna.tsinghua.edu.cn/simple tensorflow
```

当然，在`PyCharm`中也支持安装第三方包

![image-20220812143414982](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202208121434116.png)

![image-20220812143705510](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202208121437658.png)

或者在`PyCharm`中直接使用`import`语句导入包再按`Alt+Enter`也会进行自动导包

# 17. Json

`JSON`是一种轻量级的数据交互格式，可以按照`JSON`指定的格式去组织和封装数据，本质上是一个带有特定格式的字符串，是不同编程语言通信的中转站

面对结构复杂的`JSON`，可以通过[Json格式化工具](http://www.ab173.com/)查看结构

```json
// Json格式如下👇
// 单个Json的格式与Python中的字典dict类似
{"name":"tom","age":18}		
// 多个Json实则是列表+字典
[{"name":"tom","age":18},{"name":"tom","age":18}] 
```

```python
# 使用Json需要导入内置模块
import json		
# data是一个列表字典
data = [{"name": "tom", "age": 18}, {"name": "mike", "age": 20}]
# 通过json.dumps将列表字典转变为json对象，ensure_ascii=False确保中文能够正常显示
json_str = json.dumps(data, ensure_ascii=False)

# s是一个长着Json样子的字符串
s = '[{"name": "tom", "age": 18}, {"name": "mike", "age": 20}]'
# 通过loads方法将字符串转换为list对象，当然只有{}包围时也可以转为dict对象
l = json.loads(s)
```

# 18. 其他常用方法

## 18.1 图片拼接成视频

```python
def pic_to_video(images_folder,output_video):
    # 图片序列文件夹路径和输出视频文件名
#     images_folder = '../draw_pic/user01_fluorescent/compress_0.3s'
#     output_video = '../draw_pic/user01_fluorescent/compress_0.3s/output_video.mp4'

    # 获取文件夹中的所有图片文件
    image_files = [f for f in os.listdir(images_folder) if f.endswith('.jpg')]

    # 获取第一张图片的尺寸，作为视频帧大小
    first_image = cv2.imread(os.path.join(images_folder, image_files[0]))
    frame_size = (first_image.shape[1], first_image.shape[0])

    # 设置视频参数
    fourcc = cv2.VideoWriter_fourcc(*'XVID')
    fps = 10

    # 创建视频写入对象
    out = cv2.VideoWriter(output_video, fourcc, fps, frame_size)

    # 遍历图片文件，将每张图片写入视频
    for image_file in image_files:
        image_path = os.path.join(images_folder, image_file)
        frame = cv2.imread(image_path)
        out.write(frame)

    # 释放资源
    out.release()
    cv2.destroyAllWindows()
```

## 18.2 格式转换

### npy👉mat

```python
from scipy.io import savemat
import numpy as np
# 加载npy文件
data = np.load('xxx.npy')
# key为张量在matlab中的变量名,data为张量数据
savemat('xxx.mat', {'key': data})
```

### mat👉npy

```python
# 加载MAT文件
mat_data = scipy.io.loadmat('xxx.mat')
# 将数据转换为NumPy数组【key为数据在matlab中的变量名】
np.array(mat_data['key'])  
```

## 18.3 文件路径操作

```python
# 得到当前代码文件所在的目录绝对路径
current_dir = os.getcwd()
# 获取current_dir的上一层目录路径
dvs_gesture_dir = os.path.dirname(current_dir)
# 将events_csv与dvs_gesture_dir指向的路径进行拼接
events_csv = os.path.join(dvs_gesture_dir, 'events_csv')
# 得到events_csv路径下的所有文件名信息，封装成list
os.listdir(events_csv)
```

## 18.4 以逗号为分隔符保存为txt

```python
# 假设你有一个包含数据的二维列表
data = [
    ['Name', 'Age', 'City'],
    ['John Doe', '30', 'New York'],
    ['Jane Smith', '25', 'San Francisco'],
    ['Bob Johnson', '35', 'Los Angeles']
]

# 指定保存的文件路径
file_path = 'data.txt'

# 打开文件以写入数据
with open(file_path, 'w') as file:
    # 遍历数据并将其写入文件
    for row in data:
        # 使用逗号分隔符并写入一行
        file.write(','.join(row) + '\n')
```

```python
# HP趋势滤波
_, trend = sm.tsa.filters.hpfilter(y)
```

# 19. 参数化执行

`argparse` 模块是 Python 标准库中的一个模块，用于处理命令行参数。

```python
import argparse
# 创建实例对象，用于添加相关参数信息
# 当使用 -h 参数时显示 description 信息 
parser = argparse.ArgumentParser(description='描述你的程序用途')
```

parser.add_argument()：添加参数

+ -x,--xxx：首两位参数，参数短名称，参数长名称，可以只写其中一个，二者等价
+ default：指定参数默认值，默认为 None
+ choices：tuple 保存指定选项，当参数不为这些选项时报错
+ type：指定参数类型
+ help：说明当前参数作用

```python
parser.add_argument('-d', '--denoising', 
                    default='none', 
                    choices=('stcf', 'knoise', 'none'),
                    type=str
                    help='Denoising filter choice')
# 解析参数
args = parser.parse_args()
# 可以通过访问参数短名称得到输入内容
args.denoising
```

