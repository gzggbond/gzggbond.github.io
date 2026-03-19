---
title: Pandas库使用手册
date: 2024-03-31 08:41:49
excerpt: Pandas以Numpy为基础，借力Numpy模块在计算方面性能高的优势，基于matplotlib，能够简便的画图，同时拥有独特的数据结构，常用于处理结构化数据
tags:
  - 数据分析
categories:
  - [Python,数据分析]
---

`Pandas`以`Numpy`为基础，借力`Numpy`模块在计算方面性能高的优势，基于`matplotlib`，能够简便的画图，同时拥有独特的数据结构，常用于处理结构化数据

# 1. 数据结构

> `Pandas`中一共有三种数据结构，分别为：`Series`、`DataFrame`和`MultiIndex`，其中`Series`是**一维数据结构**，`DataFrame`是**二维的表格型数据结构**，`MultiIndex`是三维的数据结构

## 1.1 Series

> `Series`是一个类似于一维数组的数据结构，它能够保存任何类型的数据，比如整数、字符串、浮点数等，主要由一组数据和与之相关的索引两部分构成【像字典一样】

### Series创建

`pd.Series(data=None, index=None, dtype=None)` 

+ `data`：传入的数据，可以是`ndarray`、`list`等
+ `index`：索引，必须是唯一的，且与数据的长度相等。如果没有传入索引参数，则默认会自动创建一个从`0-(N-1)`的整数索引
+ `dtype`：数据的类型

```python
import pandas as pd
import numpy as np
# 生成0到3【不包括】之间的整数，索引为默认索引【从0开始】
a = pd.Series([5,6,7,8])
print(a)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142054391.png" alt="image-20230414205440304" style="zoom:80%;" />

```python
# 指定索引
a = pd.Series(data=[5,6,7,8], index=[1, 2, 3, 4])
print(a)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142056995.png" alt="image-20230414205629951" style="zoom:80%;" />

```python
# 通过字典数据创建
color_count = pd.Series({'red':100, 'blue':200})
print(color_count)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142057603.png" alt="image-20230414205759573" style="zoom:80%;" />

### Series属性

> 为了更方便地操作`Series`对象中的索引和数据，`Series`中提供了两个属性`index`和`values`

```python
# 数据接上
color_count.index 
# 结果 
Index(['red', 'blue'], dtype='object')

color_count.values
# 结果
array([100, 200], dtype=int64)

# 可以用索引访问值 
color_count[1]
# 结果
200
```

### Series实例方法

+ `isnull()`：当前索引对应值存放的为`None`时返回`True`，反之为`False`【`notnull()`有类似用法】

  ```python
  a = pd.Series([1,None,3])
  print(a.isnull())
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142107242.png" alt="image-20230414210727207" style="zoom:80%;" />

## 1.2 DataFrame

> `DataFrame`是一个类似于二维数组或表格【如`excel`】的对象，既有行索引，又有列索引 ，其每一列就是一个`Series`

+ 列索引，表名不同列，纵向索引`columns`，`axis=0`
+ 行索引，表明不同行，横向索引`index`，`axis=1`

### DataFrame的创建

`pd.DataFrame(data=None, index=None, columns=None)`

+ `index`：行标签。如果没有传入索引参数，则默认会自动创建一个从`0-(N-1)`的整数索引
+ `columns`：列标签。如果没有传入索引参数，则默认会自动创建一个从`0-(N-1)`的整数索引

```python
import pandas as pd
import numpy as np

# 3行3列，大小处于70-100之间
score=np.random.randint(70,100,[3,3])
# 构造列索引序列
subjects = ["语文", "数学", "英语"]
# 添加列索引
data = pd.DataFrame(score, columns=subjects)
data
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142116131.png" alt="image-20230414211655093" style="zoom:80%;" />

```python
# 也可以以字典的形式创建
df = pd.DataFrame({'month': [1, 4, 7, 10], 
                   'year': [2012, 2014, 2013, 2014], 
                   'sale':[55, 40, 84, 31]})
df
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142118808.png" alt="image-20230414211815771" style="zoom:80%;" />

```python
# 传入二维数组进行创建
df1 = pd.DataFrame([[1,2,3]])
df2 = pd.DataFrame([[1],[2],[3]])
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307261109867.png" alt="image-20230726110936752" style="zoom: 67%;" /><img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307261110696.png" alt="image-20230726111003655" style="zoom: 67%;" />

### DataFrame的属性

| 属性名    | 说明              |
| --------- | ----------------- |
| `shape`   | `DataFrame`的维度 |
| `index`   | 行索引的列表集合  |
| `columns` | 列索引的列表集合  |
| `values`  | 所有值的集合      |
| `T`       | 转置              |

### DataFrame的索引设置

#### 修改索引值

```python
# 必须整体全部修改 
data.index/columns = [7,8,9]

# 错误修改方式 
data.index/coloums[3] = 9
```

#### 重设索引

```python
# 重置索引，True使得行索引变回默认值且删除掉原索引列；False则保留原行索引列
data = data.reset_index(drop=True)

# 使得语文列变成行索引，也可以传入列表使得多列成为一个索引
data.set_index('语文')
```

### DataFrame实例方法

+ `value_counts()`：统计当前列各元素个数【降序排列】

  ```python
  # 统计data的num1中各元素的个数
  data['num1'].value_counts()
  ```

+ `head/tail(x)`：显示前/后`x`行内容，默认为`5`

+ `date_range('2021-01-01', periods=4)`：生成从2021/01/01开始往后4天的日期数据

+ `drop_duplicates(subset=['A'])`：如果两行在列 A'上的值相同，则其中一行将被保留，而另一行将被删除。

# 2. 基本数据操作

## 2.1 索引操作

> `DataFrame`采用索引取值时采用**先列后行**的方式；直接使用切片操作时只能对**行**切片，得到的依旧是 DataFrame

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041436.png" alt="image-20220903124332488" style="zoom: 67%;" />

```python
# 取出第1行往后的数据，在此基础上再次取第1行往后数据
df[1:][1:]
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202309161636197.png" alt="image-20230916163633095" style="zoom:67%;" />

```python
# 取出month这列【实则是Series】第1行往后数据
df['month'][1:]
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202309161638576.png" alt="image-20230916163802546" style="zoom:67%;" />

```python
# 利用loc方法可以按照行名列名【包括当前行列】实现切片，并且是先取行再取列
df.loc[:1,:"year"]
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041735.png" alt="image-20220903125002716" style="zoom:67%;" />

```python
# 利用iloc方法实现按照索引切片，同样是先取行再取列
df.iloc[:1,:2]
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041817.png" alt="image-20220903142135759" style="zoom:67%;" />

## 2.2 赋值操作

```python
# 对一整列赋值
df.month=1	# 方法1
df['month']=1	# 方法2
# 对某个具体部分赋值【注意先列后行】
df["month"][1]=3
```

## 2.3 排序

### DataFrame排序

`df.sort_values(by=, ascending=)`

+ `by`：指定排序参考的键
+ `ascending`：默认升序，`False`时降序

```python
# 按照year关键字从小到大排序
df.sort_values(by='year')
# 先按照year关键字从小到大排序，再按照sale排序
df.sort_values(by=['year','sale']) 
```

```python
# 按照索引排序
df.sort_index()
```

### Series排序

`series.sort_values(ascending=)`

+ `series`没有`by`参数，因为其只有一列
+ `DataFrame`的其中一列就是一个`Series`，可以通过下标取出
+ 同样由`sort_index()`方法按照索引进行排序

```python
# 取出year这一行进行排序
df['year'].sort_values(ascending=True)
```

# 3. DataFrame运算

## 3.1 算术运算

`add/sub(other)`

> 进行数学运算加上【减去】具体的一个数字

```python
# year列全部加1
df['year'].add(1)
```

## 3.2 逻辑运算

```python
# 返回以此条件为依据的布尔值
df["month"]>2
# 同时这些布尔值可以帮助我们筛选元素
df[df["month"]>2]
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041813.png" alt="image-20220903153421213" style="zoom:50%;" />

`query(expr)`

+ `expr`：查询语句字符串
+ 可以放入多个条件，使得查询更为精确

```python
# 查找year为2014且month为4的行
df.query("year==2014 & month==4")
```

`isin(values)`

> 判断某个值是否在`values`集合中，同样可以用于筛选元素

```python
# 查询month值是1或7的行
df[df['month'].isin([1,7])]
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041962.png" alt="image-20220903154526380" style="zoom:67%;" />

## 3.3 统计运算

`describe()`

```python
# 展示每一列的基本数据信息
df.describe()
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041103.png" alt="image-20220904110431251" style="zoom: 67%;" />

### 统计函数

> 对于单个函数去进行统计的时候，坐标轴还是按照默认列“`columns`” (`axis=0`, `default`)，如果要对行需要指定(`axis=1`)

```python
# 求出每一列的最大值
df.max(axis=0)
# 输出
month      10
year     2014
sale       84
dtype: int64

# 最小值，标准差，方差，中位数，众数，平均数，总数，最大值下标，最小值下标
min()/std()/var()/median()/mode()/mean()/sum()/idxmax()/idxmin()
```

+ `agg()` 方法（缩写自 "aggregate"）来对每个分组应用一个或多个聚合函数，以生成汇总统计信息

  ```python
  # 按照shop_name分组后，对price这列使用sum聚合函数且新列命名为sum
  data.groupby(["shop_name"])["price"].agg(['sum'])
  ```

### 累计统计函数

```python
# 计算month前n个数的合并记录
df["month"].cumsum()
# 输出
0     1		# 前1个数的和，1
1     5		# 前2个数字的和，1+4=5
2    12		# 前3个数字的和，1+4+7=12
3    22		# 前4个数字的和，1+4+7+10=22
Name: month, dtype: int64
```

```python
# 含义同理，cumprod()是累乘积
cummax()/cummin()/cumprod()		
```

`apply(func, axis=0)`

+  `func`：自定义函数
+  `axis=0`：默认是列，`axis=1`为对行运算

```python
df[['month', 'year']].apply(lambda x: x.max() - x.min(), axis=0)
# 输出
month    9
year     2
dtype: int64
```

# 4. 文件的读取与存储

> 我们的数据大部分存在于文件当中，所以`Pandas`会支持复杂的`IO`操作，`Pandas`的`API`支持众多的文件格式，如`CSV`、`SQL`、`XLS`、`JSON`、 `HDF5`【常用的是`CSV`和`HDF5`】

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041950.png" alt="image-20220904132423081" style="zoom:50%;" />

## 4.1 CSV

`read_csv()`

+ `filepath_or_buffer`：文件路径
+ `sep`：分隔符，默认用"`,`"隔开
+ `usecols`：指定读取的列名，列表形式
+ `skiprows`：读取时跳过的行数
+ `header`：指定将哪一行作为列名。默认为 `0`，表示使用第一行作为列名。如果没有列名，则设置为 `None`。
+ `names`：自定义列名

```python
# 读取文件,并且指定只获取'month', 'year'指标 
data = pd.read_csv('df.csv', usecols=['month', 'year'])
```

`to_csv()`

> 此方法也可以写入txt文件

+ `path_or_buf`：文件路径
+ `sep`：分隔符，默认用"`,`"隔开 
+ `columns`：选择需要的列索引
+ `header`：是否写进列索引值【默认为`True`】
+ `index`：是否写进行索引
+ `lineterminator`：末尾插入字符
+ `mode`：'`w`'：重写， '`a`' 追加

```python
# 将文件写到当前目录下
df.to_csv(path_or_buf='./df.csv',mode='w')
```

## 4.2 HDF5

`pandas.read_hdf(path_or_buf，key =None，** kwargs)`

> 从`h5`文件当中读取数据，使用是需要`python`确保安装了`tables`模块

+ `path_or_buffer`：文件路径 
+ `key`：读取的键【当前要存取的`DataFrame`的名字】

```python
data2 = pd.read_hdf(path_or_buf='data.h5',key='data')
```

`DataFrame.to_hdf(path_or_buf, key, **kwargs)`

> 用法与读取方法类似，不过此处是`DataFrame`对象进行操作

```python
# 将data写入当前目录下
data.to_hdf(path_or_buf='../data.h5', key='data')
```

注意：优先选择使用`HDF5`文件存储

HDF5在存储的时候支持压缩，使用的方式是`blosc`，这个是速度最快的也是`pandas`默认支持的，使用压缩可以提磁盘利用率，节省空间 ，`HDF5`还是跨平台的，可以轻松迁移到`hadoop`上面

# 5. 缺失值处理

```python
score = np.random.randint(40, 60, [3, 4])
subjects = ["语文", "数学", "英语", "政治"]
stus = ["同学"+str(i) for i in range(score.shape[0])]
df = pd.DataFrame(data=score, columns=subjects, index=stus)
df["物理"]=np.NaN	# 手动添加物理列，并将其值置空
df		# 在jupyter上输出df
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304081637100.png" alt="image-20220905154427131" style="zoom:67%;" />

+ 判断数据中是否包含`NaN`

  + `pd.isnull(df)`

    <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041117.png" alt="image-20220905154653690" style="zoom:67%;" />

  + `pd.notnull(df)`

    执行结果与`isnull`正好相反

+ 删除存在的缺失值`dropna()`

  | Parameters | 说明                                                         |
  | ---------- | ------------------------------------------------------------ |
  | axis       | `0`为行 `1`为列【与常用规则相反】，`default 0`，数据删除维度 |
  | how        | `any`：删除带有`NAN`的行；`all`：删除全为`NaN`的行           |
  | thresh     | `int`，保留至少 `int` 个非`NaN`行                            |
  | subset     | `list`，在特定列缺失值处理                                   |
  | inplace    | `bool`，是否修改源文件                                       |

  ```python
  # 删除至少有一个元素为NaN的行【修改axis参数可以使其变为列】
  df.dropna()
  # 删除所有元素为NaN的行【修改axis参数可以使其变为列】
  df.dropna(how="all")
  # 保留至少有2个非NaN的行
  df.dropna(thresh=2)
  # 该列中若有NaN值，则将该值对应的行删除
  df.dropna(subset=['物理'])
  # inplace设置为True时则是对原数据进行删除
  ```

+ 如果缺失值没有使用`NaN`标记，比如使用"`?`" ，则我们处理的思路为先替换‘`?`’为`np.nan`，然后继续处理【`df.replace(to_replace=, value=)`】

# 6. 数据离散化

**连续属性的离散化就是在连续属性的值域上，将值域划分为若干个离散的区间，最后用不同的符号或整数值代表落在每个子区间中的属性值**

+ 假如有人的身高数据：165，174，160，180，159，163，192，184
+ 假设按照身高分几个区间段：`150~165, 165~180,180~195`

这样我们将数据分到了三个区间段，我可以对应的标记为矮、中、高三个类别，实现了数据的离散化

`pd.qcut(data, q)`

+ `data`：要进行分组的列，如学科中的"数学"列
+ `q`：要**自动划分**为`q`组

`series.value_counts()`

> 统计相同值的个数，配合`qcut`使用则是统计相同区间的个数

```python
p_change=df["数学"]
# 自动划分为两组
qcut=pd.qcut(p_change,2)
# 统计每一组的数量
qcut.value_counts()

# 输出
(44.999, 49.0]    2		# 在此区间的有两个数
(49.0, 59.0]      1
Name: 数学, dtype: int64
```

`pd.cut(data, bins)`

+ data：待划分的series数据
+ bins：自定义划分区域

```python
p_change=df["数学"]
# 自定义区域为(45,50]，(50,60]
bins=[45,50,60]
# 根据此区域进行划分统计
cut=pd.cut(p_change,bins)
# 统计相同区间的个数
cut.value_counts()

# 输出
(45, 50]    1
(50, 60]    1
Name: 数学, dtype: int64
```

## one-hot 编码

> 把每个类别生成一个布尔列，这些列中只有一列可以为这个样本取值为`1`，其又被称为独热编码

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041618.png" alt="image-20220905175351153" style="zoom:67%;" />

`pandas.get_dummies(data, prefix=None)`

+ `data`：已经划好区间的`series`
+ `prefix`：区间列名前缀，起提示作用

```python
pd.get_dummies(cut,prefix="rise")
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304081637565.png" alt="image-20220905180018612" style="zoom: 67%;" />

# 7. 数据合并

`pd.concat([data1, data2], axis=1)`

+ 列表中是需要合并的数据
+ `axis`：表示按照列方向【1】合并还是行方向【0】合并【取值与一般用法相反】

```python
pd.concat([df,df2],axis=0)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041179.png" alt="image-20220905204225586" style="zoom:67%;" />

`pd.merge(left, right, how='inner', on=None)`

> 可以指定按照两组数据的共同键值对合并

+ `left/right`：要合并的两个`DataFrame`

+ `how`：按照什么方式连接【`left`，`right`，`outer`，`inner`】，默认内连接

  + 内连接`inner`：合并后有`NaN`的行会被清除

    ![image-20220905212438387](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041544.png)

  + 外连接`outer`：完全进行合并，有NaN的行也会进行保留

    ![image-20220905212504290](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041573.png)

  + 左连接`left`：左边部分完全保留，若右边有不能匹配的地方则填入`NaN`

    ![image-20220905212526242](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041547.png)

  + 右连接`right`：右边部分完全保留，若左边有不能匹配的地方则填入`NaN`

    ![image-20220905212542829](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041394.png)

+ `on`：指定的共同键

# 8. 交叉表与透视表

## 8.1 交叉表

> 交叉表用于计算一列数据对于另外一列数据的分组个数(用于统计分组频率的特殊透视表) 

`pd.crosstab(...)`

+ `index`：要在行中分组的值
+ `columns`：要在列中分组的值
+ `values`：根据因子聚合的值数组，需指定`aggfunc`
+ `aggfunc`：如指定，还需指定`value`
+ `normalize`：将所有值除以值的总和进行归一化 ，为`True`时候显示百分比

```python
df = pd.DataFrame({'A': [1, 2, 2, 2, 2],
                   'B': [3, 3, 4, 4, 4],
                   'C': [1, 1, np.nan, 1, 1]})
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041081.png" alt="image-20220906152253741" style="zoom:67%;" />

```python
# B的3和4出现时，对应A的1和3出现时的次数
pd.crosstab(df['A'],df['B'])
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041741.png" alt="image-20220906152328750" style="zoom:67%;" />

```python
# B的3和4出现时，对应A的1和3出现时的C列值之和
pd.crosstab(df['A'],df['B'],values=df['C'],aggfunc=np.sum)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041748.png" alt="image-20220906152357553" style="zoom:67%;" />

## 8.2 透视表

> 透视表是将原有的`DataFrame`的列分别作为行索引和列索引，然后对指定的列应用聚集函数 

`pd.pivot_table(...)`

+ `data`：`DataFrame`对象
+ `values`：要聚合的列或列的列表
+ `index`：数据透视表的`index`，从原数据的列中筛选
+ `columns`：数据透视表的`columns`，从原数据的列中筛选
+ `aggfunc`：用于聚合的函数，默认为`numpy.mean`，支持`numpy`计算方法
+ `fill_value`：用于替换缺失值的值
+ `margin`：添加所有行/列
+ `dropna`：不包括条目为`NaN`的列，默认为`True`
+ `margin_name`：当`margin`为`True`时，将包含总计的行/列的名称

```python
# 列由key组成，行由data组成，行列相同的项利用sum函数进行计算形成新值
pd.pivot_table(df, values = 'values', index = 'date', columns = 'key', aggfunc=np.sum)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041928.png" alt="image-20220906150854514" style="zoom:67%;" />

```python
# data和 key共同构成行索引，索引完全相同的部分利用len计算并放入新values中
pd.pivot_table(df, values = 'values', index = ['date','key'], aggfunc=len)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041042.png" alt="image-20220906150935761" style="zoom:67%;" />

**透视表是一种进行分组统计的函数，而交叉表是特殊的透视表，当只统计分组频率时更方便**

# 9. 分组与聚合

`DataFrame.groupby(key, as_index=False)` 

+ `key`：分组的列数据，可以多个

```python
col = pd.DataFrame({'color': ['white', 'red', 'green', 'red', 'green'], 'object': [
                   'pen', 'pencil', 'pencil', 'ashtray', 'pen'], 'price1': [5.56, 4.20, 1.30, 0.56, 2.75], 'price2': [4.75, 4.12, 1.60, 0.75, 3.15]})
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041580.png" alt="image-20220906154127119" style="zoom:67%;" />

```python
# 按照color进行分组，并且对一个分组中的多个数据取平均值
col.groupby(['color'])['price1'].mean()
# 输出
color
green    2.025
red      2.380
white    5.560
Name: price1, dtype: float64
```

# 10. 常用操作

> 下图为示例 DataFrame，名称为 **data**，后续将直接演示，不再说明

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304142041580.png" alt="image-20220906154127119" style="zoom: 67%;" />

## 遍历DatFrame每一行

```python
for index,item in data.iterrows():
    print('第一行索引：',index)
    # 得到的item是Series格式，可用数字索引或者列名索引直接访问元素
    print('第一行内容：',list(item))
    break
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307261122238.png" alt="image-20230726112235195" style="zoom: 80%;" />

## 删除指定行/列

```python
# 删除 object, color 两列，inplace参数若设置为True则会对原数据修改
data.drop(['object','color'],axis=1)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307261135335.png" alt="image-20230726113547292" style="zoom: 67%;" />

```python
# 删除行索引为0，2的两行元素
data.drop([0,2],axis=0)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202307261136901.png" alt="image-20230726113636857" style="zoom:67%;" />

## 按行拼接

```python
pd.concat([data,data],ignore_index=True)
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202308041116248.png" alt="image-20230804111642050" style="zoom: 50%;" />

## 固定间隔求均值

```python
y = pd.DataFrame([...])
# 将 index 分为200组，存储在新列 group
y['group'] = y.index // 200  
# 假设原数据列名为y，按照group分组对y取平均值
res = y.groupby('group')['y'].mean()
```



