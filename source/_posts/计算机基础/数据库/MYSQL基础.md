---
title: Mysql基础
date: 2024-08-24 13:27:50
excerpt: 本文对于Mysql数据库的基本用法做了系统性总结，持续更新
tags:
  - 数据库
categories:
  - [计算机基础,数据库]
---

# Dos相关命令 #

```sql
# 需要进入MYSQL的bin目录下执行
# 连接到MYSQL服务，注意-P为大写
mysql -h 主机IP -P 端口 -u 用户名 -p密码 

# 主机IP为本地localhost，端口3306，用户名root，密码admin
mysql -h localhost -P 3306 -u root -padmin
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311231096.png" alt="image-20230331123135048" style="zoom:80%;" />

# 1. 创建数据库 #

```sql
# 可以往“数据库名”后面空格添加其他一些设置属性
create database 数据库名;
```

+ `character set 字符集`：设置数据库编码字符集，默认为`utf8`
+ `collate utf8 字符集校对规则`：设置字符集校对规则，常用的有`utf8 bin`【区分大小写】，`utf8 general ci`【不区分大小写，默认为此】

# 2. 查看、删除数据库 #

```sql
# 查看当前使用的数据库
select database();	
# 查看数据库
show databases;  
# 查看创建这个数据库时使用的语句
show create database 数据库名称;  
# 删除一个数据库
drop database 数据库名; 
```

# 3. 数据库备份 #

> 同样需要在管理员身份运行`DOS`并进入`bin`目录才能执行语句

```sql
# 备份数据库到指定文件夹
mysqldump -u 用户名 -p密码 -B 数据库1 数据库2 数据库n  > D:\\文件名.sql
# 备份具体数据库中的某些表
mysqldump -u 用户名 -p密码 数据库 表1 表2 表n > D:\\文件名.sql
# 在mysql服务下使用，可以将sql文件的内容全部执行即恢复数据
source sql文件路径;
```

# 4. 创建表 #

```sql
# 如果变量中有关键字需要用''将关键字圈起来
create table 表名(
变量名 类型, 
# 若要添自增(auto_increment)等附加功能可以添加在同一行
……
)[character set 字符集 collate 校对规则 engine 存储引擎]; 
```

## 建表关键字

> 用法都是在**建表时，加在变量类型后边**，顺序没啥要求

+ `default xxx`：设置默认值，当插入数据无此列值时，会自动用默认值`xx`填充
+ `not null`：标注了此关键字的列插入数据时列值不允许为`NULL`【其可以的`default`配合，不传数据时就用`default`填充】

+ `auto_increment`自增长

  + 一般来说自增长是和`primary key`配合使用的

  + 自增长也可以单独使用【但是需要配合`unique`】

  + 自增长修饰的字段为整数型的

  + 自增长默认从`1`开始，也可以通过如下命令修改起始值

    ```sql
    alter table 表名 auto_increment = 新的开始值;
    ```

  + 如果添加数据时，给自增长字段【列】指定的有值，则以指定的值为准。不过一般来说如果指定了自增长就按照自增长的规则来添加数据

# 5. 数据类型 #

## 5.1 数值型 ##

> 在能满足需求的情况下，尽量选择存储空间小的，`int`和`double`可以满足现实绝大多数要求

| 数据类型  | 含义         | 对应范围                                                     |
| --------- | ------------ | ------------------------------------------------------------ |
| tinyint   | 极小整数     | $-128 \le x\le 127$                                          |
| smallint  | 小整数       | $-32768 \le x\le 32767$                                      |
| mediumint | 中等整数     | $-8388608 \le x\le 8388607$                                  |
| int       | 大整数       | $-2147483648 \le x\le 2147483647$                            |
| bigint    | 超大整数     | $-9223372036854775808 \le x\le 9223372036854775807$          |
| float     | 单精度浮点数 | $负数：-3.402823466\times 10^{38} \le x\le -1.175494351\times 10^{-38} \\正数：1.175494351\times 10^{-38} \le x\le 3.402823466\times 10^{38}$ |
| double    | 双精度浮点数 | $负数：-1.7976931348623157\times 10^{308} \le x\le -2.2250738585072014\times 10^{-308} \\正数：2.2250738585072014\times 10^{-308} \le x\le 1.7976931348623157\times 10^{308}$ |
| decimal   | 精确小数     | 小数点前可以指定不大于65的位数，小数点后可以指定不大于30的位数，不产生误差 |

## 5.2 字符型 ##

| 数据类型 | 含义             | 对应范围                          |
| -------- | ---------------- | --------------------------------- |
| char     | 固定长度字符串   | 长度 $\le$ 255个字符              |
| varchar  | 可变长度字符串   | 1-65532**字节**，字符数取决于编码 |
| text     | 长文本字符串     | 长度 $\le$ 65535个字符            |
| longtext | 极长的文本字符串 | 长度 $\le$ 4294967295个字符       |

### 字符型使用细节

+ `char(4)`里的`4`表示字符数【最大`255`】，而不是字节数，不管是中文还是字母都是放`4`个，按字符计算

  `varchar`是按照字节编码的，不同的字符集对应的字节数量不同，因此`size`最大不一定为`65532`

+ `char(4)`是定长的，即使只插入`aa`，也会占用分配的`4`个字符的空间

  `varchar(4)`是变长的，就是说，如果插入了`aa`，实际占用空间大小并不是`4`个字符，而是按照实际占用空间来分配。同时`varchar`本身还需要占用`1-3`个字节来记录存放内容长度，所以实际大小会多上`1-3`字节

+ 查询速度 `char > varchar`

  如果数据是定长，推荐使用`char`，比如`md5`的密码，身份证号码等

  如果一个字段的长度是不确定，我们使用`varchar`，比如留言，文章

+ 在存放文本时，也可以使用`Text`数据类型。可以将`Text`列视为`varchar`列，注意`Text`不能有默认值【即不需要在定义时加小括号填数值】，如果希望存放更多字符，可以选择`longtext`

## 5.3 日期型 ##

日期常用类型数据有三种：

1. `date`：形如`2023-03-29`
2. `datetime`：形如`2023-03-29 11:55:33`
3. `timestamp`：形如形如`2023-03-29 11:55:33`【形式与`datetime`一样，目前不清楚使用上二者有何差别】

```sql
CREATE TABLE t(
# 生日
birthday DATE, 
# 年月日时分秒
job_time DATETIME, 
# 时间戳，默认为当前时间，表每次insert和update后时间戳也更新
login_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP() ON UPDATE CURRENT_TIMESTAMP()
);
INSERT INTO t(birthday,job_time) VALUES('2020-01-01','2022-01-01 10:10:10');
SELECT * from t;
```

<img src="https://s2.loli.net/2022/03/30/Xy9plgVkO82TPUh.png" alt="image-20210525160059190" style="zoom:50%;" />

# 6. 操作表 #

```sql
# 查看当前表结构
desc 表名;	
```

## 6.1 修改表结构 ##

```sql
# 表改名
Rename table 原名 to 新名;  
# 改表字符集
alter table 表名 character set 字符集; 
# 删除表
drop table 表名; 
# 清空表内容，但是不改变结构
truncate table 表名;

# 修改表编码
alter table 表名 convert to character set utf8;
# 查询数据库的所有表名
select table_name from information_schema.`TABLES` where TABLE_SCHEMA = '数据库名';
```

### 6.1.1 增列结构 ###

```sql
# 默认新列加在最后一列位置
alter table 表名 add 列名 列类型 列参数;
# 新列加在指定列后
alter table 表名 add 列名 列类型 列参数 after 指定列名; 
# 新列加在第一列位置
alter table 表名 add 列名 列类型 列参数 first; 
```

### 6.1.2 修改列类型 ###

```sql
# 修改列类型
Alter table 表名 modify 列名 新类型 新参数;		
# 修改列名及列类型
Alter table 表名 change 旧列名 新列名 新类型 新参数; 
```

### 6.1.3 删除列结构 ###

```sql
alter table 表名 drop 列名;
```

## 6.2 增删改查 ##

+ **关键字顺序**：select --> from --> where --> group by --> having --> order by --> limit
+ **执行顺序**：from --> where --> group by --> having --> select --> order by --> limit

### 6.2.1 增insert

```sql
# 若不声明列名，则默认插入所有列
# insert ignore into xxx表示当数据已存在则忽略此插入操作
insert into 表名 (列名) values (对应列名数据，可一次加多行数据);  
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303282010917.png" alt="image-20210525162714105" style="zoom: 67%;" />

### 6.2.2 删delete

```sql
# 删除满足条件的【整行】数据，若无表达式则删除所有记录
delete from 表名 where 表达式;  
```

### 6.2.3 改update

```sql
# 若没有where语句则会修改所有列内容
update 表名 set 列名1=新值1,列名2=新值2 where 表达式(限定范围);
# replace函数将某列值替换为另一个值
update 表名 set col=replace(col,old_val,new_val);
```

### 6.2.4 查select ###

```sql
# 列名为*则选择所有列，在列名前加distinct可以去重【完全一样才去重】
select 列名 from 表名 where 表达式;
# select中的列名可看作变量，因此可以进行运算，运算后的结果加as可以产生新列名
# 将stu表中的 score列值与score2列值相加，为其命名为temp并和name一起显示
select name,(score+score2) as temp from stu; 
```

## 6.3 where过滤

`where`可以根据条件过滤掉不符合条件的内容，若要使用正则表达式则需要用关键词`regexp`而不是等号

```sql
# comment列以是或者求开头的数据
where comment regexp '^[是求]';
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303282013627.png" alt="image-20210525172158394" style="zoom: 67%;" />

## 6.4 like模糊查询

`like`是模糊查询，`%`表示`0`到多个任意字符，`_`表示单个任意字符

```sql
# 查看stu表中姓 张 的所有同学
select * from stu where name like '张%';
```

## 6.5 order by结果排序

+ `order by`指定排序的列，排序的列既可以是表中的列名，也可以是`select`语句后指定的列名。
+ `order by`可以按照多列排序，用逗号隔开

```sql
# 将stu表按照score列逆序排序【默认为asc，即顺序】
select * from stu order by score desc;
```

## 6.6 group by分组统计 ##

+ `group by 列名`会把同样列值的分为一组，同时我们应该想到，分组后其余非分组条件的列会被压缩，因此需要用聚合函数【比如`sum`求和】对这些列值进行处理
+ `group by`同样可以按照多列分组，用逗号隔开

```sql
# 查看stu表中生日相同的学生中的得分平均值
select avg(score) from stu group by birthday;
```

+ group_concat(exp1,exp2... [order by xxx] seperator '分隔符')

  > 将分组后合并的列按照上述规则拼接为一整行，默认分隔符为逗号

  ```sql
  # 在分组后，将每组的course_name列按照','分隔符进行连接变为一整行
  SELECT GROUP_CONCAT(course_name SEPARATOR ', ') AS courses
  FROM students
  GROUP BY student_name;
  ```

## 6.7 having二次过滤

`having`是对过滤结果的补充过滤，通常放在`group by`后面，**可以使用聚合函数**，依旧以组作为操作单位

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291352455.png" alt="image-20230329135250420" style="zoom: 80%;" />

```sql
# 查看stu表中生日相同的学生中的得分平均值超过60的分数
select avg(score) as mean_score from stu
group by birthday
having mean_score>60;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291356945.png" alt="image-20230329135643913" style="zoom:80%;" />

## 6.8 limit分页显示

`limit length`：从头开始取`length`条数据

`limt startIndex,length`：从`startIndex`【开头为`0`】开始取`length`条数据

`limit length offset startIndex`：含义同上👆

## 6.9 函数 ##

> **函数可以对整列使用，可以对单独传入的一个值使用**

### 6.9.1 数学函数

+ `sum(列名)` ：将此列所有满足条件的部分**求和**

+ `count(*)`是返回**行总数**；`count(列名)`是返回具体列有多少条记录，会去除`NULL`值

+ `avg(列名)` ：将此列所有满足条件的部分求**平均值**【用法与`sum`基本一致】

+ `mod(列名)` ：将此列所有满足条件的部分**求余**【用法与`sum`基本一致】

+ `max(列名)/min(列名)` ：将此列所有满足条件的部分求**最大/小值**【用法与`sum`基本一致】

+ `least(列名1，列名2...)`：返回**多列的最小值**

+ `abs(列名)` 会将此列所有满足条件的部分求**绝对值**【用法与`sum`基本一致】

+ `ceiling(列名)` 会将此列所有满足条件的部分**向上取整**【用法与`sum`基本一致】

+ `floor(列名)` 会将此列所有满足条件的部分**向下取整**【用法与`sum`基本一致】

+ `rand(列名)`：对整列进行**随机取值**【传入参数后每次运算结果一致，如果不传入参数得到真正随机数】，范围`[0,1]`

+ `bin(列名)/hex(列名)` 会将此列所有满足条件的部分**转换为二/十六进制表达**【用法与`sum`基本一致】

+ `conv(列名num,from_base,to_base)` ：将`num`这列**由`from_base`进制转换为`to_base`进制**，如果传入的`num`是一个数则对这个数进行转换

  ```sql
  # 将数字8由十进制转换为二进制表达
  SELECT CONV(8,10,2);
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303282051623.png" alt="image-20230328205137591"  />

+ `format(列名num,保留的小数位x)`：将`num`这列保留`x`为小数【四舍五入】，如果传入的`num`是一个数则对这个数进行操作

  ```sql
  # 将数字2.553保留一位小数【四舍五入】
  SELECT FORMAT(2.553,1);
  ```

  ![image-20230328205220846](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303282052877.png)
  
+ `pi()`：返回圆周率

### 6.9.2 字符串函数 ###

+ `charset(列名)`：返回此列字符集【比如`binary，utf8`之类】

+ `concat(列名1,',',列名2)`：按照列名1，逗号`,`，列名2的形式进行连接【 NULL 值不忽略】

  `concat_ws(':', 列名1, 列名2, ...)`：按照列名1，分号`:`，列名2的形式进行连接【 NULL 值忽略】

  `group_concat()`：一组内的所有值结合为一个字符串，然后返回这个字符串

+ `l/rpad(nums,2,'0')`：保证 nums 列的长度为 2 ，缺失的部分在前/后方填充 0

+ `instr(列名1，列名2)`：返回“列名`2`”内容在“列名`1`”内容中首个出现位置起始下标【从`1`开始计数】，没有则返回`0`

  ```sql
  # 查看 j 在 jerjry 中首次出现的位置
  SELECT INSTR('jerjry','j');
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291135304.png" alt="image-20230329113534224" style="zoom: 80%;" />

+ `ucase(列名)/lcase(列名)`：将整列转换为大/小写

+ `left(列名,长度len)/right(列名,长度len)`：从最左/右边当前列名对应内容的前`len`个字符

  ```sql
  # 取 jerry 的前3个字符
  SELECT LEFT('jerry',3);
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291138683.png" alt="image-20230329113854655" style="zoom:80%;" />

+ `length(列名)`：返回当前列名对应内容的字节长度

  > char_length 返回字符长度

+ `replace(列名，字符串str，字符串str2)`：用`str2`替换列名对应内容中的`str`

+ `strcmp(列名1，列名2)`：按`ASKII`码比较两列大小，若列`1`小返回`-1`，相等返回`0`，较大则返回`1`

+ `substring(列名，起始位置pos，长度len)`：从`pos`【开头为`1`】起取列名对应的内容长度`len`【`len`参数不填默认取全部】的内容

  > 可以接受负数索引

+ `ltrim(列名)/rtrim(列名)/trim(列名)`：去除列名对应内容中前端/后端/全部空格

+ `repeat(字符c,次数time)`：重复显示`c`字符`time`次

+ `reverse(字符串c)`：对`c`进行反转

+ `locate(substr, str)`：找到 substr 在 str 中第一次出现的下标（从 1 开始），不存在时返回 0

+ `substring_index(字符串,',',count)`：将逗号作为分隔符并取第一个分割的字符串

  > 如果 count 是正数，则从左边开始计算，返回第 count 个分隔符前的子字符串；
  > 如果 count 是负数，则从右边开始计算，返回倒数第 |count| 个分隔符后的子字符串

  ```sql
  # 返回22,33，因为倒数第二个是22，返回22（包括）往后所有元素
  SELECT SUBSTRING_INDEX('11,22,33',',',-2)
  # 返回11,22，因为第二个是22，返回22（包括）往前所有元素
  SELECT SUBSTRING_INDEX('11,22,33',',',2)
  ```

### 6.9.3 日期函数 ###

+ `current_date()`：得到**当前日期**

  ```sql
  SELECT CURRENT_DATE();
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291154148.png" alt="image-20230329115407106" style="zoom:80%;" />

+ `current_time()`：得到**当前时间**

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291155325.png" alt="image-20230329115502286" style="zoom:80%;" />

+ `current_timestamp()`：得到**当前时间戳**

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291156024.png" alt="image-20230329115602997" style="zoom:80%;" />

+ `date(datetime)`：返回`datetime`类型【一般是时间戳】中的日期

  ```sql
  SELECT DATE('2023-03-29 11:55:33');
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291159042.png" alt="image-20230329115935014" style="zoom:80%;" />

+ `date_format(date,format)`：格式化日期

  ```sql
  # 格式化submit_time列
  select date_format(submit_time,"%Y%m%d")
  ```

  + `%Y`：四位数年份
  + `%m`：两位数的月份（01-12）
  + `%d`：两位数的天数（01-31）
  + `%H`：24小时制的小时（00-23）
  + `%h`：12小时制的小时（01-12）
  + `%i`：两位数的分钟数（00-59）
  + `%s`：两位数的秒数（00-59）

+ `last_day(date)`：获得当前date月份的最后一天

  ```sql
  # 结果2024-01-31
  select last_day('2024-01-01')
  ```
  
+ `date_add(date1,interval d_val d_type)`：为`date1`【一般为`datetime`类型】加上`da_val d_type`的时间，其中`d_type`是单位

  > `d_type`可选取值：`year,minute,second,day`

  ```sql
  # 为2023-03-29 11:55:33加上一天
  SELECT DATE_ADD('2023-03-29 11:55:33',INTERVAL 1 DAY);
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291210982.png" alt="image-20230329121034943" style="zoom:80%;" />

+ `date_sub(date1,interval d_val d_type)`：用法与`date_add`类似，不过其是减去一个时间

+ `timediff(date1,date2)`：两个时间差【单位是时分秒】

  `datediff(date1,date2)`：两个日期差【单位是天】

  > date1 - date2

+ `timestampdiff(unit,date1,date2)`：两个时间差（单位是时间戳），可以通过 unit 指定时间差单位

  > date2 - date1

  ```sql
  # 得到两个日期的时间戳差再转换为second形式
  select timestampdiff(second,'2020-02-02 20:22:23','2020-02-03 20:22:22')
  ```

+ `last_day(date1)`：返回当前日期这个月的最后一天

+ `year(date1)/month/day/hour/minute/second`：返回当前日期对应的年/月/日/时/分/秒

+ `unix_timestamp()`：返回当前时间的时间戳

  ```sql
  # DUAL是MYSQL中的空表
  SELECT UNIX_TIMESTAMP() FROM DUAL;
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291257202.png" alt="image-20230329125730159" style="zoom:80%;" />

+ `from_unixtime(时间戳，格式)`：将时间戳转换为对应格式，不指定格式时使用默认格式【默认格式与`datetime`一致】，通过默认格式就能满足需求，若需要自定义格式可参考如下博客👇

  [具体日期格式](https://blog.csdn.net/weixin_50853979/article/details/124873585?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168006379816800211527869%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168006379816800211527869&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-124873585-null-null.142^v76^control_1,201^v4^add_ask,239^v2^insert_chatgpt&utm_term=FROM_UNIXTIME%28%29&spm=1018.2226.3001.4187)

  ```sql
  # 通过UNIX_TIMESTAMP()得到当前时间戳，再使用FROM_UNIXTIME转换为对应格式
  SELECT FROM_UNIXTIME(UNIX_TIMESTAMP())  FROM DUAL;
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291302426.png" alt="image-20230329130227979" style="zoom:80%;" />

#### 实例 ####

假设`release_time`列存放的是新闻发布时间，请查询在`2`天内发布的新闻时间

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291248462.png" alt="image-20230329124802428" style="zoom:67%;" />

```sql
# 如果发布时间加上2天大于等于现在的时间，则可以断定新闻是两天内发布的
SELECT * FROM mes WHERE DATE_ADD(release_time,INTERVAL 2 DAY)>=NOW();
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291252838.png" alt="image-20230329125217808" style="zoom:67%;" />

### 6.9.4 加密函数 ###

`md5(列名)`：对此列进行`md5`加密，从而无法在数据库中直接看到原始数据

```sql
# 从emp表中取出pw列中值为123的行【密码是被MD5加密过的】
select * from emp where pw=MD5('123'); 
```

### 6.9.5 流程控制函数 ###

+ `if(exp1,exp2,exp3)`：如果`exp1`为`True`，则返回`exp2`，否则返回`exp3`

  判断是否为空要用关键字`is`或者`is not`

  ```sql
  # emp表中的name列值如果是NULL就用tom，否则用marry
  select IF(name IS NULL,'tom','marry') from emp;
  ```

+ `ifnull(exp1,exp2)`：如果`exp1`不为`NULL`，则返回`exp1`，否则返回`exp2`

+ `select case when expr1 then expr2 when expr3 then expr4 else expr5 ... end`：如果`expr1`为`TRUE`，则返回`expr2`，如果`expr3`为`True`则返回`expr4`，否则返回`expr5`

### 6.9.6 窗口函数

> 窗口函数执行在 group by 之后

+ `ROW_NUMBER() OVER()` 是一种在 SQL 中用于为结果集中的行分配**唯一**序号的窗口函数。它通常与 `ORDER BY` 子句一起使用，以便在对结果集排序的基础上为行分配序号

  > dense_rank() 用法功能类似，不过面对特征一致的两行数据，其在分配序号时会分配相同序号

  ```sql
  # 假设有一个名为 employees 的表，包含员工的姓名和工资信息
  # 此操作为每个员工按照工资从高到低的顺序分配序号
  SELECT emp_name,salary,ROW_NUMBER() OVER(ORDER BY salary DESC) AS row_num
  FROM employees;
  ```

  ```python
  # 以department作为分组依据，再对salary降序后分配序号
  SELECT emp_name,department,salary,
  ROW_NUMBER() OVER(PARTITION BY department ORDER BY salary DESC) AS row_num
  FROM employees
  # 取出相同department中，salary拍在前二的数据
  where row_num <=2
  ```

+ `LEAD(date, 1)`：这是一个窗口函数，用于获取当前行之后指定偏移量的行的值。在这里，`LEAD(date, 1)`表示获取当前行后面第1行（即下一行）的日期值。如果没有下一行，则返回NULL。

  ```sql
  SELECT user_id,date AS current_date,
    # 按照user_id分类后，取出每组date顺序排列的第二个date
    LEAD(date, 1) OVER (
        PARTITION BY user_id
        ORDER BY date ASC
    ) AS next_date
  FROM user_activity;
  ```

# 7. 多表查询 #

> 多表查询是指基于两个和两个以上的表查询，在实际应用中，查询单个表可能不能满足需求

**默认情况下，当两个表查询时，规则如下：**

1. 从第一张表中取出一行，与第二张表的每一行进行组合，返回结果【含有两张表的所有列】

2. 一共返回的记录数=第一张表行数`*`第二张表行数

3. 这样多表查询默认处理返回的结果，称为笛卡尔集

   ```sql
   # 如下👇查询会产生笛卡尔积
   select * from tb1,tb2;
   ```

4. **==解决多表的关键就是要写出正确的过滤条件where==**

**多表查询的条件 $\ge$ 表的个数-1，否则会出现笛卡尔集**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292031227.png" alt="image-20230329203154185" style="zoom:67%;" />

## 7.1 natural join自然连接

> 自然连接不填写连接条件，默认会把列名相同的列作为连接条件，且连接后会去除重复列

```sql
# tb1与tb2的empid列名相同，默认被当作连接条件
SELECT * FROM tb1 NATURAL JOIN tb2;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292048891.png" alt="image-20230329204839126" style="zoom:67%;" />

## 7.2 join内连接 ##

> 关键字`join`等价于`inner join`，是内连接的意思，内连接需要写连接条件，两个表中满足条件的行会组成新行

```sql
# tb1里empid只要与tb2里的empid相同，就把tb2这一行抓过来组成新行
# 通过起别名可以使得表达式可便于阅读
SELECT * FROM tb1 as x JOIN tb2 as y ON x.`empid`=y.`empid`;
# 自然select也可以自定义显示的列
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292015602.png" alt="image-20230329201547549" style="zoom:67%;" />

**由于`tb2`里没有`empid=a107`的行，因此`tb1`中`empid=a107`的行在查询结果中被抛弃了**

```sql
# 当tb1与tb2需要连接的关键字名称正好相同时可以使用using(连接的列名)
# 使用using还能去除连接产生的重复列
SELECT * FROM tb1 JOIN tb2 USING(empid);
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292023430.png" alt="image-20230329202329385" style="zoom:80%;" />

```sql
# 连接自然也能连接多个表
# 内连接确保查出来的每一行都是原表格的数据，不会填充NULL
# 即使tb3有empid=107的行，此处也无法查询出来，因为tb2没有empid=107的行
SELECT * FROM tb1 AS x 
JOIN tb2 AS y ON x.empid=y.empid
JOIN tb3 AS z ON x.empid=Z.empid;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292034731.png" alt="image-20230329203426669" style="zoom:80%;" />

## 7.3 left/right join外连接 ##

> 关键词`left/right outer join`等价于`left/right join`，是左/右外连接的意思
>
> 外连接会根据左/右来保留某张表的所有行，即使on条件不满足也不会过滤主表数据，而是会把另一张表在当前行不符合on条件的行至空

**左外连接**

> 左表的部分被全部保留，如果连接的右表匹配不上则会被置为`NULL`

```sql
SELECT * FROM tb1 AS x 
LEFT JOIN tb2 AS y
ON x.empid=y.empid;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292057213.png" alt="image-20230329205636994" style="zoom:80%;" />

**右外连接**

> 右表的部分被全部保留，如果连接的左表匹配不上则会被置为`NULL`【其实把左连接的tb1和tb2位置换一下就等价于右连接了】

```sql
SELECT * FROM tb1 AS x 
RIGHT JOIN tb2 AS y
ON x.empid=y.empid;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292102836.png" alt="image-20230329210240780" style="zoom:80%;" />

在一个`SQL`语句里左/右外连接最后不要混用，容易造成混乱

## 7.4 自连接

> 自连接就是通过起别名的方式【**自连接必须起别名**】自己与自己连接，产生的实际上是笛卡尔积

```sql
# tb2自己连自己
SELECT * FROM tb2 AS X 
JOIN tb2 AS Y;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292111851.png" alt="image-20230329211117788" style="zoom: 67%;" />

# 8. 子查询 #

> 子查询是指嵌入在其他`SQL`语句中的`select`语句，也叫嵌套查询

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303292031227.png" alt="image-20230329203154185" style="zoom:67%;" />

## 8.1 单列子查询 ##

> 所谓单列子查询，指的是子查询中由`select`得到的中间值只有一列

### 8.1.1 in关键字

**子查询中尽量不使用=，而是使用关键字in**

> 因为可以使用`=`的场景一定可以使用`in`，但是可以使用`in`的场景不一定能使用`=`

```sql
# 查找tb1中销售额最高的人
# 语句会最底层往上执行，流程如下👇
# 1. 查询tb1中最大销售额s
# 2. 查找tb1中最大销售额s对应的empid
# 3. 根据empid找到tb2中对应的人
SELECT * FROM tb2 WHERE empid 
IN(SELECT empid FROM tb1 WHERE sales
IN(SELECT MAX(sales) FROM tb1));
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301800594.png" alt="image-20230330175956521" style="zoom:80%;" />

### 8.1.2 all和any关键词 ###

> 将子查询结果用`all`包起来，表示**所有的查询结果**；`any`包起来表示**任意一个查询结果**

**需要注意的是，搭配`all`和`any`关键值时不能使用`in`，只能用`=`【因为`in`和`= any()`代表同个意思】**

```sql
# 显示tb1表中销售额大于a104任意一个月份销售额的行，且不显示empid为a104的行
# a104在tb1中共两条数据，最小销售额为93，所以实则是等价于找销售额高于93的行
SELECT * FROM tb1 WHERE sales >
ANY(SELECT sales FROM tb1 WHERE empid='a104')
AND empid <> 'a104';
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301844363.png" alt="image-20230330184457309" style="zoom:80%;" />

```sql
# a104在tb1的两条数据中销售额最高为181，所以实则等价于找销售额高于181的行
SELECT * FROM tb1 WHERE sales >
ALL(SELECT sales FROM tb1 WHERE empid='a104')
AND empid <> 'a104';
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301849459.png" alt="image-20230330184911404" style="zoom:80%;" />

### 8.1.3 exists关键字

> `exists`中文意思是存在，使用的时候不需要指定特定列名，其会自动比对

```sql
# 1. 查出tb1表中empid在tb2表中也有出现的行【empid, sales, mon】，记作表q
# 2. tb2表三列中empid与查询出来的结果中的empid正好格式一致，所以这两列会被拿来比对
# 3. 只显示在表q出现过的empid在tb2表中empid对应的行【这段不好描述，试一下就知道了】
SELECT * FROM tb2 WHERE 
EXISTS(SELECT * FROM tb1 WHERE tb1.`empid`=tb2.`empid`);
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301830118.png" alt="image-20230330183029074" style="zoom: 80%;" />

`exists`的使用与`select`查询出来的内容无直接联系，仅仅与表真实行有关系，如下👇

```sql
# select语句中并未出现empid，但是不妨碍exists的使用
SELECT name,age FROM tb2 WHERE 
EXISTS(SELECT sales FROM tb1 WHERE tb1.`empid`=tb2.`empid`);
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301832731.png" alt="image-20230330183228691" style="zoom:67%;" />

`not exists`用法就是`exists`的反面，即`exists`查询不到的内容，用了`not exists`就能查询到

```sql
# exists的反面
SELECT * FROM tb2 WHERE 
NOT EXISTS(SELECT * FROM tb1 WHERE tb1.`empid`=tb2.`empid`);
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301836938.png" alt="image-20230330183628893" style="zoom:80%;" />

## 8.2 多列子查询 ##

> 子查询结果有多列，则将参数一一对应

```sql
# 查询tb2表中name为小帅或者小美的信息【此处只是为了演示语法，实际上一条语句可以搞定】
SELECT * FROM tb2 WHERE (empid,NAME,age) 
IN(SELECT * FROM tb2 WHERE NAME='小帅' OR NAME='小美');
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301900606.png" alt="image-20230330190057551" style="zoom:80%;" />

## 8.3 其他

> 子查询还可以用在`from`后，即子查询查出的内容作为新表，可以为新表起别名

```sql
# 查询tb1表中sales大于100且小于190的数据【此处同样为了演示语法，实际上一条语句可以搞定】
SELECT * FROM 
(SELECT * FROM tb1 WHERE sales > 100)temp 
WHERE temp.sales < 190;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301905250.png" alt="image-20230330190517190" style="zoom:80%;" />

# 9. 表的复制 #

## 9.1 复制结构和记录

```sql
# 复制整个b，包括结构和内容
create table a select * from b;
```

## 9.2 仅复制结构

```sql
# 创建表a，其与b有同样的结构
create table a like b;
```

## 9.3 仅复制记录

```sql
# 将b的内容对应着插入a【需要保证a和b的结构一致，变量名可以不一致】
insert into a select * from b;
# 将b中的name列复制到a中的name列
# 同理，加上where等语句可以复制符合条件的记录
insert into a(name) select name from b;
```

# 10. union合并查询 #

+ `union all` ：用于取得两个结果集的并集，不会取消重复行
+ `union`：会取消重复行

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291945679.png" alt="image-20230329194537610" style="zoom:67%;" />

```sql
# 将b拼接在a的行方向，且union关键字会去重，可以合并多个表
SELECT * FROM a
UNION
SELECT * FROM b;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303291947346.png" alt="image-20230329194710293" style="zoom:80%;" />

# 11. 约束 #

> **各约束的创建方法类似，可参考主键**

## 11.1 primary key主键

> 主键使得此列成为每行元素独一无二的标签，其关键字是`primary key`

+ `primary key`不能重复且不能为`NULL`
+ 一张表最多只能有一个主键，但可以是复合主键
+ 使用`desc`表名，可以看到`primary key`的情况

```sql
create table A(
# 添加主键方法1：在列类型后加关键字
id int primary key,  
name char(10)
);

create table A(
id int,  
name char(10),
# 添加主键方法2：在表定义最后利用关键字包裹需要建立主键的列名
primary key(id) 
);

create table A(
id int,  
name char(10),
address char(20)
# id和name构成复合主键，当两者都相同时被认为主键相同
primary key(id,name) 
);
```

## 11.2 unique唯一键 ##

+ 如果在列上定义了`not null`，那么当插入数据时，必须为列提供数据

+ 当定义了唯一约束`unique`后，该列值是不能重复的

  如果没有指定`not null`，则`unique`字段可以有多个`null`

  一张表可以有多个`unique`字段

+ `unique`不认为`NULL`是一个具体值，因此允许有多个`NULL`；当`unique`的列被`not null`修饰后，整体类似于主键

## 11.3 foreign key外键

> 外键用于定义主表与从表之间的关系，一旦建立主外键的关系，数据不能随意删除了，提高数据库的安全性

+ `innodb`类型的表才支持外键
+ 外键约束定义在从表上。外键指向的表的字段，要求是`primary key`或者是`unique`
+ 外键字段的类型要和主键字段的类型一致【长度可以不同】
+ 外键字段的值，必须在主键字段中出现过，或者为`null`【前提是外键字段允许为`null`】

```sql
# 外键添加语法
foreign key (本表字段名) references 主表名(主键名或unique字段名)
```

```sql
# 假设此时my_class数据有【1,a】【2,b】
create table my_class(
# 班级编号
id int primary key, 
name varchar(30),
);

create table my_stu(
# 学生编号
id int primary key, 
# 学生所在班级编号
class_id int, 
name varchar(30),
# 建立与主表my_class的外键关系
foreign key(class_id) references my_class(id)) 
);

# 创建后添加外键
alter table 表名1 add foreign key(表1字段) references 表名2(表2字段);
```

+ **若`my_stu`要插入数据，则`class_id`必须是1，2或`NULL`【前提是my_stu的class_id字段允许`NULL`】，否则失败**

+ **建立起外键关联后，若要删除`my_class`中`id`为1的行，则需要将`my_stu`中关联的`class_id`为1的行全部删除才行。**

  ***可以理解为，想要打大哥，得先把手下小弟打败***

# 12. 索引 #

> 在未建立索引时，查询某个数据会采取全表扫描的方式
>
> 对关键字建立索引后，实际上是用了二叉搜索树的方式对关键字进行排序，因此**搜索速度会快很多**

**由于索引采用了树的结构进行存储，因此==索引本身需要占用空间==；对更新(update)、插入(insert)、删除数据(delete)的效率也会有影响【因为==破坏了树的结构，重新调整需要时间开销==】**

## 12.1 类型 ##

+ **主键索引**：主键自动为主索引【类型为`Primary key`】
+ **唯一索引**：唯一键同样自动为`UNIQUE`索引
+ 全文索引：
+ **普通索引(Index)**：需要手动添加

## 12.2 索引添加 ##

```sql
# unique不写就是添加普通索引，写了就是添加唯一索引
create [unique] index 索引名 on 表名(列名);
# 只用于添加普通索引
alter table 表名 add index 索引名 (列名);
# 添加主键索引
alter table 表名 add primary key(列名);
```

## 12.3 索引删除 ##

```sql
# 删除索引
drop index 索引名 on 表名; 
# 删除主键索引
alter table 表名 drop primary key;
```

## 12.4 查看索引 ##

```sql
show index from 表名;
show indexes from 表名;
show key from 表名;
```

## 12.5 何时创建索引 ##

+ **较频繁作为查询条件字段应该创建索引**
+ 唯一性太差的字段不适合单独创建索引【比如性别，总共两个选项，索引没有太大意义】
+ 更新非常频繁的字段不适合创建索引
+ 不会出现在`where`子句中字段不该创建索引【无意义】

# 13. 存储过程

> 存储`stored`表示保存，过程`procedure`表示步骤，因此存储过程是将一系列步骤归纳并存储起来的集合
>
> 换个说法：将多个`SQL`语句组合成一个只需要使用命令`“CALL XX”`就能执行的集合，该集合就叫**存储过程**

```sql
# 存储过程建立语法
delimiter //
create procedure 存储过程名(参数名 数据类型) # 不需要传参则不需要填写
begin
SQL语句1;
SQL语句2;
...
end //
```

## 13.1 创建存储过程

### 13.1.1 无参数的存储过程

```sql
# 将两条select语句组合成一个存储过程p1
DELIMITER //
CREATE PROCEDURE p1()
BEGIN
SELECT * FROM tb1;
SELECT * FROM tb2;
END //
# 执行这个存储过程
CALL p1();
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303302005610.png" alt="image-20230330200558552" style="zoom:67%;" />

### 13.1.2 有参数的存储过程

```sql
DELIMITER //
# 传入的第一个参数sales是int类型，传入的第二个参数age是int类型
CREATE PROCEDURE p2(sales INT,age INT)
BEGIN
SELECT * FROM tb1 WHERE tb1.`sales`=sales;
SELECT * FROM tb2 WHERE tb2.`age`=age;
END //

CALL p2(300,40);
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303302014483.png" alt="image-20230330201406441" style="zoom:80%;" />

## 13.2 展示存储过程

```sql
# 展示语法
show create procedure 存储过程名;
```

```sql
# 显示p2的存储过程
SHOW CREATE PROCEDURE p2;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311153032.png" alt="image-20230331115321994" style="zoom:80%;" />

## 13.3 删除存储过程

```sql
# 删除语法
drop procedure 存储过程名;
```

```sql
# 删除p1存储过程
DROP PROCEDURE p1;
```

## 13.4 存储函数

> **存储函数 = 存储过程 + 返回值**，返回值可以在`select`和`update`等命令中和普通函数一样使用

**准备工作**

```sql
# 参数log_bin_trust_function_creators值设置为1才能使用存储函数
SET GLOBAL log_bin_trust_function_creators=1;
# 查看参数是否修改成功
SHOW VARIABLES LIKE 'log_bin_trust_function_creators';
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303302049092.png" alt="image-20230330204958046" style="zoom:80%;" />

```sql
# 存储函数创建语法
DELIMITER //
create function 存储函数名(参数 数据类型) returns 返回值的数据类型
begin
SQL语句
...
return 返回值/表达式;
END //
```

### 13.4.1 使用存储函数计算标准体重

$标准体重BMI =  \dfrac{体重kg}{身高^2m}$

```sql
# 函数名为standard_weight，第一个参数weight为int型，第二个参数height为double型
# 返回值为double型
DELIMITER //
CREATE FUNCTION standard_weight(weight INT,height DOUBLE) RETURNS DOUBLE
BEGIN
RETURN weight/(height * height);
END //
# 查看体重为72kg，身高为180.5cm的BMI值
SELECT standard_weight(72,1.805);
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303302052451.png" alt="image-20230330205229392" style="zoom:80%;" />

### 13.4.2 使用存储函数返回记录平均值

```sql
# 声明一个变量
declare 变量名 数据类型;
```

```sql
# 创建返回记录平均值函数sale_avg()
DELIMITER //
CREATE FUNCTION sale_avg() RETURNS DOUBLE
BEGIN
# 声明一个double类型的变量r
DECLARE r DOUBLE;
# 将计算结果放入到r中
SELECT AVG(sales) INTO r FROM tb1; 
# 将r作为返回值
RETURN r;
END //
# 返回tb1的sales列平均值
SELECT sale_avg();
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303302104225.png" alt="image-20230330210448181" style="zoom:80%;" />

## 13.5 删除存储函数

```sql
drop function 存储函数名;
```

## 13.6 显示存储函数内容

```sql
show create function 存储函数名;
```

```sql
SHOW CREATE FUNCTION standard_weight;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311157460.png" alt="image-20230331115709415" style="zoom:80%;" />

# 14. 事务 #

> 事务用于保证数据的一致性，它由一组相关的`DML【Data Manipulation Language】`语句组成，该组的`DML`语句要么全部成功，要么全部失败。如：转账就要用事务来处理，用以保证数据的一致性。

**`MYSQL`数据库控制台事务的几个重要操作**

1. `start transaction`：开始一个事务
2. `savepoint 保存点名`：设置保存点
3. `rollback to 保存点名`：回退事务
4. `rollback`：回退全部事务
5. `commit`：提交事务，所有操作均生效，执行后不能回退

```sql
# 创建表a
CREATE TABLE a( 
id INT,
);

# 开启事务
START TRANSACTION; 
# 设置保存点A
SAVEPOINT A; 
# a表中插入id=2的数据，但是此时事务未提交，因此仅仅在当前事务里添加成功，其他会话此时查询a表看不到此数据
INSERT INTO a (id) VALUES (2);
# 设置保存点B
SAVEPOINT B; 
INSERT INTO a (id) VALUES (3);
# 回滚到保存点B之前的状态，此时查看a表不会有id=3的数据
ROLLBACK TO B; 
# 回滚到事务开始前，此时查看a表甚至没有id=2的数据
ROLLBACK;
# 提交事务，由于回滚到事务开始前，因此最终提交后啥也没干
COMMIT; 
```

+ **保存点(savepoint)是事务中的点，用于取消部分事务**，当结束事务时(`commit`)会自动删除该事务所定义的所有保存点；当执行回退事务时，通过指定保存点可以回退到指定的点
+ 使用**commit**语句可以提交事务，当执行了`commit`语句后会确认事务的变化、结束事务、删除保存点、释放锁、数据生效。**当使用commit语句结束事务后，其他会话【连接】将可以看到事务变化后的新数据【所有数据正式生效】**
+ 即使开启事务也无法复原的情况：`drop database/table/view，alter table`

## 14.1 ACID特性 ##

+ **原子性**：指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生
+ **一致性**：事务必须使数据库从一个一致性状态变换到另外一个一致性状态
+ **隔离性**：多个用户并发访问数据库时，**==数据库为每一个用户开启的事务不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离==**
+ **持久性**：指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响

### 细节 ###

1. 如果不开始事务，**默认情况下**，`DML`【`Data Manipulation Language`】操作是**自动提交**的，不能回滚

   ```sql
   # 通过此操作可以将自动提交关闭，此时任何SQL语句执行后都需要commit操作才能真正执行
   # 设置为1则恢复自动提交
   set autocommit=0;
   ```

2. 如果开始一个事务但没有创建保存点也是可以执行`rollback`，**默认回滚到事务开始的状态**

3. 在事务中**可以创建多个保存点**

4. 在事务没有提交前，可以选择回退到某个保存点

5. `MYSQL`的**事务机制需要innodb的存储引擎才可以使用**，`myisam`不好使

## 14.2 事务隔离级别 ##

+ **脏读**：当一个事务**读取到另一个事务尚未提交的修改时**，产生脏读
+ **不可重复读**：同一查询在同一事务中多次进行，由于**其他提交事务所做的==修改或删除==，每次返回不同的结果集**
+ **幻读**：同一查询在同一事务中多次发生，由于**其他提交事务所做的==插入==操作，每次返回不同的结果集**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311239442.png" alt="image-20210529144612799" style="zoom: 50%;" />

```sql
# 查询当前会话隔离级别，默认为Repeatable-read
select @@tx_ioslation; 

# 假设此时同时打开了两个MYSQL界面1,2，设置界面2的隔离级别为Read-uncommitted
# 两边同时开启事务，START TRANSACTION，分别记为事务1，事务2
# 此时若事务1向表中Insert数据，即使事务1还没有Commit，事务2中select查看依然可以看到Insert的内容，这就叫脏读

# 改变事务隔离级别，必须在事务外使用
set session transaction isolation level 等级 
# 改变界面2隔离级别为读已提交
set session transaction isolation level read committed; 
# 此时事务1向表中Insert或update数据时，事务2中select都查看不到，不过一旦事务1提交之后，事务2就会看到事务1更新的数据，这叫脏读和幻读

# 改变界面2隔离级别为可重复读
set session transaction isolation level Repeatable read; 
# 此时事务1向表中Insert或update数据时，事务2中select都查看不到，即使事务1提交之后，事务2内也看不到事务1更新后的数据，只有事务2也提交后，才会看到事务1做的修改。

# 改变界面2隔离级别为可串行化
set session transaction isolation level Serializable; 
# 与可重复读不同的是，此时为若事务1对a表进行操作，则事务2也对a表进行操作时会停顿【加锁】，直至事务1提交后才解锁，事务2对a表的操作才会继续下去

# 查询系统隔离级别，默认为Repeatable-read
select @@global.tx_ioslation; 
```

# 15. 触发器

> **触发器`trigger`是一种对表执行某种操作后会触发执行其他命令的机制**
>
> 当执行`insert`，`update`和`delete`等命令时，作为触发器提前设置好的操作也会被执行。例如，创建一个触发器，当某表记录发生更新时，就以此为契机将更新的内容记录到另外一个表中

## 15.1 创建触发器

```sql
# 触发器创建语法
delimiter //
create trigger 触发器名 before/after SQL命令
on 表名 for each row
begin
# old.列名可以取到更新前的列；new.列名可以取到更新后的列
使用更新前【old.列名】或者更新后【new.列名】的处理
end //
```

### 触发器应用实例

```sql
# 复制表，使得tb2_copy与tb2有相同结构，但是为目前为空表
CREATE TABLE tb2_copy LIKE tb2;

# 创建触发器
DELIMITER //
# 触发器名称为tr1
# 对tb2表进行delete操作时会引起触发器tr1封装的相关操作
# before是在准备执行delete操作前执行触发器；after正好相反
CREATE TRIGGER tr1 BEFORE DELETE 
ON tb2 FOR EACH ROW
BEGIN
# tr1是针对表tb2的触发器，因此old.列名取到的是tb2执行delete前对应列的内容
# 此语句的意思为：将tb2删除的行插入到tb2_copy表中
INSERT INTO tb2_copy VALUES(old.empid,old.name,old.age);
END //

# 测试触发器，删除姓名为小帅与小美的行
DELETE FROM tb2 WHERE NAME='小帅' OR NAME='小美';
# 查看tb2删除之后的数据
SELECT * FROM tb2;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311149321.png" alt="image-20230331114950201" style="zoom:80%;" />

```sql
# 查看删除的数据是否插入到tb2_copy表中
SELECT * FROM tb2_copy;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311151063.png" alt="image-20230331115112022" style="zoom:80%;" />

## 15.2 查看触发器

```sql
show triggers;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311159971.png" alt="image-20230331115939916" style="zoom:80%;" />

## 15.3 删除触发器

```sql
drop trigger 触发器名;
```

# 16. 存储引擎 #

+ **Myisam引擎**：==不支持事务，也不支持外键==，但**访问速度快**，对事务完整性没有要求
+ **InnoDB引擎**：**提供了具有提交、回滚和崩溃恢复能力的事务安全**。但是比起`Myisam`存储引擎，`InnoDB`写的处理效率差一些并且会占用更多的磁盘空间以保留数据和索引
+ **Memory引擎**：使用存在内存中的内容来创建表。每个`Memory`表只实际对应一个磁盘文件，`Memory`类型的表访问非常快，因为它的**数据都是放在内存**中的，并且默认使用`HASH`索引，不过一旦`MYSQL`服务关闭，表中的数据就会丢失，表的结构不会丢失

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311239926.png" alt="image-20210529165828064" style="zoom: 67%;" />

+ 如果应用**不需要事务**，处理的只是基本的`CRUD`操作，那么**Myisam**是不二选择，速度快

+ 如果需要**支持事务，选择InnoDB**

+ **Memory**引擎就是将数据存储在内存中，由于没有磁盘`I/O`的等待，**速度极快，但由于是内存存储引擎，所做的任何修改在服务器重启后都将消失**【经典用法：用户在线状态】

  ```sql
  # 修改存储引擎
  alter table 表名 engine = 引擎名; 
  ```

```sql
# 查看建表结构可以看到存储引擎
show create table tb1;
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303302124830.png" alt="image-20230330212453762" style="zoom:80%;" />

# 17. 视图 #

> 视图是一个虚拟表，其内容由查询定义，同真实的表一样，视图包含列，其数据来自对应的真实表

**视图本质上是基表的映射【理解为两个对象指向同一个地址】，因此改变视图数据会影响到基表，基表数据改变也可能影响到视图，视图增删改查操作与普通表没有区别**

```sql
# 创建一个视图【select查出的表作为视图内容】
create view 视图名 as select语句; 
# 修改原有的视图【select查出的表作为新视图内容】
alter view 视图名 as select语句; 
# 如果视图存在就用替换掉，如果不存在就创建
create or replace view 视图名 as select语句; 
# 查看视图的创建语句
show create view 视图名; 
# 删除视图
drop view 视图名1,视图名2; 
# 查看视图结构
desc 视图名;
```

+ 创建视图后，**对应视图的只有一个视图结构性文件**【形式：视图名.`frm`】

+ 视图的数据变化会影响到基表，基表的数据变化也会影响到视图

+ **视图中可再使用视图**

+ 往视图添加数据时，只有遵守视图创建规则的数据才能添加成功

  ```sql
  # 将tb1中sales > 100的数据组合成视图v1
  CREATE VIEW v1 AS 
  SELECT * FROM tb1 WHERE sales > 100;
  # 查看v1当前数据
  SELECT * FROM v1;
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301930510.png" alt="image-20230330193002458" style="zoom:80%;" />

  ```sql
  # 往v1中添加数据，注意此时sales=90，其值小于100
  INSERT INTO v1 VALUES('a109',90,6);
  # 再次查看视图v1数据
  SELECT * FROM v1;
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301931439.png" alt="image-20230330193112385" style="zoom:80%;" />

  可以发现此条数据并未插入视图，但是却成功插入了`tb1`表，如下👇

  ```sql
  SELECT * FROM tb1;
  ```

  <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303301932429.png" alt="image-20230330193239374" style="zoom:80%;" />

## 视图好处 ##

1. **安全**：一些数据表有着重要信息，**有些字段是保密的，不能让用户直接看到**。这时就可以创建一个视图，在这张视图中只保留一部分字段。这样用户可以查询到自己需要的字段而不能直接看到保密字段
2. **性能**：关系数据库的数据常常会分表存储，使用外键建立这些表的之间关系。这是数据库查询通常会用到连接(`join`)。这样做不但麻烦，效率相对也比较低。**如果建立一个视图，将相关的表和字段组合在一起，就可以避免使用`join`查询数据**
3. **灵活**：如果系统中有一张旧表，这张表由于设计的问题即将被废弃。然而很多应用都是基于这张表，不易修改。这时可以建立一张视图，视图中的数据直接映射到新建的表。这样可以少做很多改动，也达到了升级数据表的目的

# 18. Mysql管理 #

> `mysql`中的用户都存储在系统数据库`mysql`的`user`表中

`user`表字段说明

+ **host**：允许登录的"位置"，`localhost`表示该用户只允许本机登录，也可以指定`ip`地址
+ **user**：用户名
+ **authentication_string**：是通过`password()`加密函数加密后的密码

## 18.1 创建/删除用户 ##

```sql
# 创建用户
create user 用户名 @ 允许登录位置 identified by 密码  
# 删除用户
drop user '用户名' @ '允许登录位置' 

# 不同的数据库用户，操作的库和表不相同【权限】
# 用户修改自己密码
set password=password('密码') 
# 修改他人密码，前提是有修改密码权限
set password from '用户名' @ '登录位置' =password('密码'); 
```

## 18.2 授权 ##

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311239149.png" alt="image-20210529190638849" style="zoom: 80%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311238459.png" alt="image-20210529185205545" style="zoom: 50%;" />

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202303311239615.png" alt="image-20210529185305091" style="zoom: 50%;" />

```sql
# 创建用户名为dragon，密码为123的用户
CREATE USER 'dragon'@'localhost' IDENTIFIED BY '123' ; 
# 赋予这个用户查看experiment数据库中a表的权限【其他操作不允许】
GRANT SELECT ON experiment.a to 'dragon'@'localhost';
# 将刚刚赋予的权限回收
REVOKE SELECT ON experiment.a FROM 'dragon'@'localhost';
# 删除用户
DROP USER 'dragon'@'localhost'; 
```

+ **在创建用户时，如果不指定Host，则为%，表示所有IP地址都有连接权限**
+ `create user 'xxx'@'192.168.1.%'`表示`xxx`用户在`192.168.1.*`的IP可以登录`mysql`
+ 在删除用户时，如果`Host`不是`%`，需要指定`'用户'@'host值'`

# 19. 主从复制

MySQL 主从复制是一个异步的复制过程，底层是基于 MYSQL 数据库自带的二进制日志功能。就是一台或多台 MYSQL 数据库（slave，即从库）从另一台 MYSQL 数据库（master，即主库）进行日志的复制然后再解析日志并应用到自身，最终实现从库的数据和主库的数据保持一致。MYSQL 主从复制是 MYSQL 数据库自带功能，无需借助第三方工具。

整个过程分为三步：

+ master 将改变记录到二进制日志（ binary log）
+ slave 将 master 的 binary log 拷贝到它的中继日志（relay log）
+ slave 重做中继日志中的事件，将改变应用到自己的数据库中

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202310201958910.png" alt="image-20231020195823784" style="zoom: 67%;" />

# SQLyog快捷键 #

```sql
# 运行所有查询语句
Ctrl + F9		
# 运行选中的查询语句
F9
# 注释
Ctrl + Shift + C
# 取消注释
Ctrl + Shift + R
# 刷新数据库表内容【鼠标要点击到数据库的表位置，否则默认刷新整个数据库】
F5
# 格式化选择的查询语句
Ctrl + F12
# 格式化所有的查询语句
Shift + F12
# 打开表【鼠标需要点击到对应的表】
F11
# 改变表结构【同样鼠标需要点击到对应的表】
F6
```

