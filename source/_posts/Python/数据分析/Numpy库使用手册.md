---
title: Numpy库使用手册
date: 2024-03-30 20:41:49
excerpt: Numpy是一个开源的Python科学计算库，用于快速处理任意维度的数组，支持常见的数组和矩阵操作
tags:
  - 数据分析
categories:
  - [Python,数据分析]
---

`Numpy 【Numerical Python】`是一个开源的`Python`科学计算库，用于快速处理任意维度的数组。`Numpy`支持常见的数组和矩阵操作。对于同样的数值计算任务，使用`Numpy`比直接使用`Python`要简洁的多。`Numpy`使用`ndarray`对象来处理多维数组，该对象是一个快速而灵活的大数据容器。

```python
import numpy as np
# 创建一个二维ndarray对象
score=np.array([[1,2,3],[4,5,6],[7,8,9]])
```

# 1. ndarray相对于list优势

`Numpy`专门针对`ndarray`的操作和运算进行了设计，所以数组的存储效率和输入输出性能远优于`Python`中的嵌套列表，数组越大，`Numpy`的优势就越明显。

这是因为**`ndarray`中的所有元素的类型都是相同的，而`Python`列表中的元素类型是任意的**，所以`ndarray`在存储元素时内存可以连续，而`python`原生`list`就只能通过寻址方式找到下一个元素，这虽然也导致了在通用性能方面`Numpy`的`ndarray`不及`Python`原生`list`。但在科学计算中，`Numpy`的`ndarray`就可以省掉很多循环语句，代码使用方面比`Python`原生`list`简单的多。

`Numpy`底层使用`C`语言编写，内部解除了`GIL`【全局解释器锁】，其对数组的操作速度不受`Python`解释器的限制，所以，其效率远高于纯`Python`代码。

# 2. ndarray的属性

|      属性名字      |          属性解释          |
| :----------------: | :------------------------: |
|  `ndarray.shape`   |       数组维度的元组       |
|   `ndarray.ndim`   |          数组维度          |
|   `ndarray.size`   |      数组中的元素数量      |
| `ndarray.itemsize` | 一个数组元素的长度【字节】 |
|  `ndarray.dtype`   |       数组元素的类型       |

可以调用这些属性得知`ndarray`的基本情况

# 3. ndarray的类型

| 类型           | 类型代码     | 描述                                                         |
| -------------- | ------------ | ------------------------------------------------------------ |
| `int8,uint8`   | `i1,u1`      | 有符号和无符号的`8`位整数                                    |
| `int16,uint16` | `i2,u2`      | 有符号和无符号的`16`位整数                                   |
| `int32,uint32` | `i4,u4`      | 有符号和无符号的`32`位整数                                   |
| `int64,uint64` | `i8,u8`      | 有符号和无符号的`64`位整数                                   |
| `float16`      | `f2`         | 半精度浮点数                                                 |
| `float32`      | `f4或f`      | 标准单精度浮点数                                             |
| `float64`      | `f8或d`      | 标准双精度浮点数                                             |
| `float128`     | `f16或g`     | 拓展精度浮点数                                               |
| `complex64`    | `c8,c16,c32` | 分别基于`32`位，`64`位，`128`位浮点数的复数                  |
| `complex128`   |              | 👆                                                            |
| `complex256`   |              | 👆                                                            |
| `bool`         | `?`          | 布尔值，存储`True`或`False`                                  |
| `object`       | `o`          | `Python object`类型                                          |
| `string_`      | `S`          | 修正的`ASCII`字符串类型，例如生成一个长度为`10`的字符串类型，使用`S10` |
| `unicode_`     | `U`          | 修正的`Unicode`类型，生成一个长度为`10`的`Unicode`类型，使用`U10` |

```python
import numpy as np
# 创建一个二维ndarray对象，指定元素类型为float32，dtype='f4'与之等价
score=np.array([[1,2,3],[4,5,6],[7,8,9]],dtype=np.float32)
# 使用astype()方法可以显示转换类型，tobytes()改为字节型
score = score.astype(np.float64)
```

若不指定，整数默认`int64`，小数默认`float64 `

# 4. 生成ndarray的方法

> `numpy`库的导入语句在后续例子中省略

+ `np.array(list)`：将输入数据（可以是列表、元组以及其他序列）转换为`ndarray`，如不显式指明数据类型，将自动推断；默认复制所有的输入数据

  `asarray(list)`：将输入转换为`ndarray`，但如果输入已经是`ndarray`则不再复制

  ```python
  import numpy as np
  data = np.array([[1.5, -0.1, 3], [0, -3, 6.5]])
  print(data)
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304111735046.png" alt="image-20230411173506006" style="zoom:80%;" />

+ `np.arrage(start,stop,step)`：`Python`内置函数`range`的数组版，返回一个数组

  生成范围`[start,stop)`，步长为`step`的`ndarry`，`start`默认为`0`，`step`默认为`1`

  ```python
  # 生成的数组a为：[1,3,5]
  a = np.arange(start=1, stop=6, step=2)
  ```

+ `np.linspace(start, stop, num, endpoint, retstep, dtype)`：指定起点和长度

  + `start`：数列的起始值。
+ `stop`：数列的终止值。
  + `num`：生成数列的元素个数（默认为 50）。
  + `endpoint`：是否包括终止值（默认为 `True`）。
  + `retstep`：如果为 `True`，返回数列的同时返回步长（默认为 `False`）。
  + `dtype`：生成数组的数据类型（可选参数）。

+ `np.ones((shape),dtype)`：生成指定`shape`的全`1`数组，`dtype`默认为`float64`

  `np.ones_like(arr)`：根据所给数组生成一个形状一样的全`1`数组

  ```python
  a = np.ones((2, 3), dtype='f4')
  print(a)
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304111815214.png" alt="image-20230411181548186" style="zoom:80%;" />

+ `np.zeros((shape),dtype)`：生成指定`shape`的全`0`数组，`dtype`默认为`float64`

  `np.zeros_like(arr)`：根据所给数组生成一个形状一样的全`0`数组

  使用方式如上`ones`方法👆

+ `np.empty((shape),dtype)`：生成指定`shape`的没有初始化值的空数组，`dtype`默认为`float64`

  `np.empty_like(arr)`：根据所给数组生成一个形状一样的没有初始化值的空数组

+ `np.full((shape), fill_value,dtype)`：生成指定`shape`的值全为`fill_value`的数组，`dtype`默认为`int64`

  `np.ones_like(arr)`：根据所给数组生成一个形状一样的值全为`fill_value`的数组

  ```python
  a = np.full(shape=(2, 3), fill_value=5)
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304111825496.png" alt="image-20230411182531467" style="zoom:80%;" />

+ `np.identity(n,dtype)`：生成`n*n`的矩阵，对角线元素全`1`，其余全`0`，`dtype`默认为`float64`

## 4.1 伪随机数生成

+ `np.random.randint(low,high,(size))`：生成`size`形状【`size`为`int`时一维】的数组，其值在`[low,high)`之间

  ```python
  # 生成3*3的矩阵，其值在[3,5)之间
  score = np.random.randint(3, 5, (3, 3))
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304111840761.png" alt="image-20230411184051732" style="zoom:80%;" />

+ `np.random.rand(shape) `：生成`shape`形状的数组，值为`[0，1)`内的一组均匀分布数，用法与正态分布的`randn`类似👇

+ `np.random.uniform(low=0.0,high=1.0,size=None)`：从一个均匀分布`[low,high)`中随机采样，形状与`size`中描述一致

  ```python
  score = np.random.uniform(low=0.0, high=1.0, size=(3, 3))
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304111907453.png" alt="image-20230411190739420" style="zoom:80%;" />

+ `np.random.randn(shape)`：生成`shape`形状的数组，其值是标准正态分布随机数

  ```python
  score = np.random.rand(3, 3)
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304111846906.png" alt="image-20230411184625869" style="zoom:80%;" />

+ `np.random.normal(loc=0.0, scale=1.0, size=None)`：传入`μ`和从而得到正态分布

  + `loc`：`float`类型，正态分布的均值【$\mu$，此值是正态分布峰值横坐标】
  + `scale`：`float`类型，正态分布的标准差【σ，此值越大图形越胖，越小越高】
  + `size`：输出的`shape`，默认为`None`，只输出一个值

  ```python
  # 生成均值为1.75，标准差为1的正态分布数据，5个
  score = np.random.normal(1.75, 1, 5)
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304111855074.png" alt="image-20230411185547043" style="zoom:80%;" />

**numpy.random中其他伪随机数方法**

| 函数          | 描述                                           |
| ------------- | ---------------------------------------------- |
| `seed`        | 确定随机数生成器的种子                         |
| `permutation` | 返回一个序列的随机排列或返回一个随机排列的范围 |
| `shuffer`     | 对一个序列就地随机排列                         |
| `binomial`    | 产生二项分布的样本值                           |
| `beta`        | 产生`Beta`分布的样本值                         |
| `chisquare`   | 产生卡方分布的样本值                           |
| `gamma`       | 产生`Gamma`分布的样本值                        |

# 5. ndarray形状修改

+ `np.reshape(shape)/resize(new_shape)/T`

  > `reshape`返回一个具有相同数据域，但形状不一样的视图；`resize`则是在原数组上进行修改；`T`则是`ndarray`的一个属性，表示转置后的`ndarray`

  ```python
  # 在转换形状的时候，一定要注意数组的元素匹配
  arr = np.array([[1,2,3],[4,5,6]])
  # 将arr由2行3列修改为3行2列
  arr = arr.reshape([3,2])
  # 将arr修改为3列，行数通过内部计算得到，目前待定
  arr.reshape((-1,3)) 
  ```

+ **np.flatten(order='C')**

  > 二维展开为一维数组，order 参数为 F 时按列展开，默认为 C 即按行展开

  ```python
  arr.flatten(order='F')
  ```

+ 

# 6. ndarray进行数据筛选

## 6.1 逻辑运算

```python
# 生成4行6列的矩阵，取值在[3,5)之间
arr = np.random.randint(3,5,[4,6])
# arr取行下标为2之后的行以及列下标为4【不包括】之前的列构成新数组arr1
# 逗号左边是行的取值情况，右边是列的取值情况，分别切片
arr1 = arr[2:,:4]
# arr1中大于3的赋为True，其余赋为False，得到一个新的布尔类型数组
bool_arr = arr1 > 3
# 将arr1中大于3的部分赋值为1
arr1 [ bool_arr ] = 1
```

## 6.2 通用判断函数

```python
# 每行代表不同学生，每列代表不同科目
score = np.array([[70, 60, 81], [99, 97, 94], [30, 52, 79]])
# 判断前两名同学的成绩是否全及格，全部及格返回True
flag = np.all(score[:2, :] > 60)
# 判断前两名同学的成绩是否有大于80分的，有一个就返回True
np.any(score[:2, :] > 80)
```

## 6.3 三元运算符

```python
# arr1中大于1的部分赋1，其余赋0
np.where(arr1 > 1, 1, 0)
# 复合逻辑需要结合np.logical_and和np.logical_or使用
# arr1中大于60且小于90的部分赋1，其余赋0
np.where(np.logical_and(arr1 > 60, arr1 < 90), 1, 0)
# arr1中大于60或者小于50的部分不变，其余赋0
np.where(np.logical_or(arr1 > 60, arr1 < 50), arr1, 0)
```

# 7. ndarray的运算

> 任何在两个等尺寸数组之间的算术操作都应用了逐元素操作的方式

```python
a=np.array([[2,3],[4,5]])
b=np.array([[1,2],[3,4]])

# 对应元素相乘（除，加，减）
a *[/,+,-] b

# a矩阵每个元素取倒数
1 / a

# a矩阵每个元素的0.5次方
a ** 0.5

# 同形状数组比较，返回布尔数组 
a > b
```

## 7.1 常用一元通用函数

> 通用函数`ufunc`是一种对`ndarray`中的数据执行元素级运算的函数。你可以将其看做简单函数（接受一个或多个标量值，并产生一个或多个标量值）的矢量化包装器
>
> 一元通用函数指对一个`ndarray`操作的通用函数

```python
# 四舍五入，保留2位小数
np.round(arr, decimals=2)
```

| 函数                      | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| `abs,fabs`                | 计算整数、浮点数或复数的绝对值。对于非复数值，可以使用更快的`fabs` |
| `sqrt`                    | 计算各元素的平方根（等价于`arr ** 0.5`）                     |
| `square`                  | 计算各元素的平方，（等价于`arr **2`）                        |
| `exp`                     | 计算各元素的自然指数值 $e^x$ ，其中`x`是数组元素             |
| `log, log10, log2, log1p` | 分别为底数为`e`, 底数为`10`, 底数为`2`，底数为`1+x`的`log`   |
| `sign`                    | 计算各元素的正负号：`1`(正数)，`0`(零)，`-1`(负数)           |
| `ceil`                    | 计算大于等于该值的最小整数                                   |
| `floor`                   | 计算小于等于该值的最大整数                                   |
| `rint`                    | 将各元素值四舍五入到最接近的整数，保留`dtype`                |
| `modf`                    | 将数组的小数和整数部分以两个独立数组的形式返回【两个接收值】 |
| `isnan`                   | 返回一个表示“哪些值是`NaN`”的布尔型数组                      |
| `isfinite,isinf`          | 分别返回一个表示“哪些元素是有穷的(非`inf`，非`NaN`)”或“哪些元素是无穷的”的布尔型数组 |
| `cos,cosh,arccos`         | 普通型，双曲型三角函数和反三角函数【`sin,tan`有类似用法】    |
| `logical_not`             | 计算各元素`not x`的真值，相当于`~arr`                        |

## 7.2 常用二元通用函数

| 函数                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `add`                                                        | 将数组中对应的元素相加                                       |
| `subtract`                                                   | 从第一个数组中减去第二个数组中的元素                         |
| `multiply`                                                   | 数组元素相乘                                                 |
| `divide,floor_divide`                                        | 除法或向下圆整除法【丢弃余数】                               |
| `power`                                                      | 对第一个数组中的元素`A`，根据第二个数组中的相应元素`B`，计算 $A^B$ |
| `maximum,fmax`                                               | 保留同一位置中的最大值，`fmax`将忽略`NaN`元素级的最小值计算【`min`用法类似】 |
| `mod`                                                        | 对第一个数组中的元素`A`，根据第二个数组中的相应元素`B`，计算`A%B` |
| `copysign`                                                   | 将第二个数组中的值的符号复制给第一个数组中的值               |
| `greater,greater_equal`<br>`less,less_equal`<br>`equal,not_equal` | 执行元素级的比较运算，最终产生布尔型数组<br>相当于运算符`>、>=、<、<=、==、!=` |
| `logical_and`<br>`logical_or`<br>`logical_xor`               | 执行元素级的真值逻辑运算<br>相当于运算符`&、|、^`            |

## 7.3 常用统计函数

| 方法            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| `sum`           | 对数组中全部或某轴向【`axis`参数】的元素求和，零长度`sum`为`0`<br>布尔值存储`True`强制为`1`，`False`为`0`，因此可用于统计`True`个数 |
| `mean`          | 算数平均数，零长度的数组的`mean`为`NaN`                      |
| `std,var`       | 分别为标准差和方差，自由度可调（默认为`n`）                  |
| `min,max`       | 最大值和最小值                                               |
| `argmin,argmax` | 分别为最大和最小元素的索引                                   |
| `cumsum`        | 所有元素的累计和                                             |
| `cumprod`       | 所有元素的累计积                                             |

## 7.4 常用集合操作

| 方法          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| `unique`      | 计算数组中的唯一元素，并返回有序结果【一维】                 |
| `intersect1d` | 计算`x`和`y`中的公共元素，并返回有序结果【一维】             |
| `union1d`     | 计算`x`和`y`的并集，并返回有序结果【一维】                   |
| `in1d`        | 得到一个表示“`x`的元素是否包含于`y`”的布尔型数组             |
| `setdiff1d`   | 集合的差，即元素在`x`中且不在`y`中                           |
| `setxor1d`    | 集合的对称差，即存在于一个数组中但不同时存在于两个数组中的元素 |

## 7.5 线性代数

> `Numpy`的线性代数`*`是矩阵逐元素乘积，而不是矩阵点乘积【点乘是`@`】，`T`属性是矩阵转置

### 7.5.1 numpy常用线代方法

| 方法    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| `diag`  | 以一维数组的形式返回方阵的**主**对角线元素，或将一维数组转换为方阵（非对角线元素为`0`) |
| `dot`   | 矩阵点乘【效果与`@`运算符一致】                              |
| `trace` | 计算对角线元素和【迹】                                       |

### 7.5.2 numpy.linalg常用方法

> `numpy.linalg`拥有一个矩阵分解的标准函数集以及其他常用函数，例如求逆和行列式求解等

| 方法    | 说明                                    |
| ------- | --------------------------------------- |
| `det`   | 计算矩阵行列之                          |
| `eig`   | 计算方阵的特征值和特征向量              |
| `inv`   | 计算方阵的逆矩阵                        |
| `pinv`  | 计算矩阵的`Moore-Penrose`伪逆           |
| `qr`    | 计算`QR`分解                            |
| `svd`   | 计算奇异值分解【`SVD`】                 |
| `solve` | 解线性方程组`Ax = b`，其中`A`为一个方阵 |
| `lstsq` | 计算`Ax = b`的最小二乘解                |

# 8. 加载文本

`np.loadtxt()` 是 NumPy 库中用于从文本文件加载数据的函数。它允许您从文本文件中加载数据，并将其存储为 NumPy 数组

+ `fname`：要加载的文件的路径或文件对象。
+ `dtype`：返回数组的数据类型。默认为 float。可以使用 Python 中的任何有效数据类型，如 float、int、str 等。
+ `comments`：表示注释的字符。默认为 `#`。在加载数据时，以此字符开头的行将被视为注释，并被忽略。
+ `delimiter`：用于分隔数据的字符串。默认为 None，表示根据空白字符（空格、制表符等）进行分隔。
+ `skiprows`：要跳过的行数。默认为 0，表示不跳过任何行。
+ `usecols`：要读取的列的索引或列名列表。默认为 None，表示读取所有列。
+ `unpack`：如果为 True，则返回转置后的数组。默认为 False。
+ `ndmin`：返回数组的最小维数。默认为 0，表示返回一个一维数组。
+ `encoding`：指定文件的编码格式。默认为 'bytes'。
+ `max_rows`：要读取的最大行数。默认为 None，表示读取所有行。

# 9. 字符串相关

+ np.char.xxx
  + capitalize(arr)：将ndarray中所有字符串规范为首字母大写
  + add()：对两个字符串数组进行逐元素的字符串连接。
  + multiply()：对字符串数组中的每个字符串进行重复操作。
  + center()：返回一个字符串数组，其中字符串居中并用空格填充至给定的宽度。
  + capitalize()：将字符串数组中每个字符串的首字母大写。
  + title()：将字符串数组中每个字符串的每个单词的首字母大写。
  + lower()：将字符串数组中每个字符串转换为小写。
  + upper()：将字符串数组中每个字符串转换为大写。
  + split()：将字符串数组中的每个字符串按照指定的分隔符分割。
  + strip()：去掉字符串数组中每个字符串两侧的空格。
  + replace()：将字符串数组中的每个字符串中指定的子字符串替换为另一个字符串。
  + join()：将字符串数组中的每个字符串连接成一个单个字符串。
+ 
