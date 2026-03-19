---
title: Java常用API
date: 2024-09-03 14:27:23
excerpt: 本文总结了数据结构使用Java语言实现算法时常用的API调用方式
tags:
  - 数据结构
categories:
  - [计算机基础,理论知识,数据结构]
---

# 1. 栈和队列LinkedList #

> 使用`push`插入元素时【栈】，头部元素`peek`为栈顶元素<br>使用`addLast`插入元素时，头部元素`peek`为队首元素
>
> 通常创建一个对象专精一个数据结构，不要串用

## 1.1 栈常用方法 ##

```java
// 栈顶插入元素
push(ele)   
// 返回栈顶元素并弹出    
pop()   
//返回栈顶元素但不弹出
peek()    
```

## 1.2 队列常用方法 ##

```java
// 头插
addFirst(ele)		
// 尾插
addLast(ele)
// 获取队列头元素    
getFirst()		
// 获取队列尾元素    
getLast()			
// 获取头元素并弹出
poll()			
// 获取尾元素并弹出【对于栈插入，最先进入的为尾】     
pollLast()		 
// 删除指定索引元素    
remove(index)		
// 返回首次出现某元素的索引
indexOf(ele)				
```

## 1.3 通用方法 ##

```java
// 清空
clear()				
// 返回大小    
size()			
// 判断是否为空
isEmpty()					
// 得到指定索引元素【栈是从栈顶数下来】
get(index)			
// 改变某索引处的元素
set(index,ele)		
```

# 2. 链表ArrayList #

```java
// 默认将元素添加到链表尾，可以通过index添加到指定位置
add(ele)		
// 移除指定位置元素
remove(index)		
// 修改指定位置元素
set(index,element)		
// 得到指定位置元素
get(index)			
// 返回第一次/最后一次出现某元素的索引
indexOf/lastIndexOf(ele)	
//返回大小/是否为空    
size()/isEmpty()			
// 链表变数组    
toArray()			
// 字符串形式输出【若要输出自定义形式，可以遍历后用StringBuilder拼接】
toString()	
// 使用Stream流将列表转为数组
int[] arr = list.stream().mapToInt(Integer::intValue).toArray();
```

# 3. 集合Set #

> `TreeSet`相较于`HashSet`多了排序功能【排序通过重写`Comparator`的方法实现】，因此用`TreeSet`就行

```java
// 集合如果不存在元素ele，则添加此元素
add(ele)			
// 清空
clear()				
// 查询指定元素是否存在，存在返回true
contains(ele)		
// 判空
isEmpty()				
// 如果指定元素在此set中则移除
remove(ele)			
// 返回元素数量
size()			
// 返回一个大于等于当前元素的最小元素【屋顶上一个】，不存在返回null
ceiling(ele)		
// 返回一个小于等于当前元素的最大元素【地板下一个】，不存在返回null
floor(ele)			
// 返回此set中严格大于给定元素的最小元素,不存在返回null
higher(ele)			
// 返回此set中严格小于给定元素的最大元素，不存在返回null
lower(ele)			
// 返回第一个元素
first()			
// 返回最后一个元素
last()					
```

```java
// 差集
removeall()
// 交集
retainAll()
// 并集
addAll()
```



# 4. 映射Map #

> 理由同`Set`，只讲`TreeMap`，可根据关键字排序
>

`LinkedHashMap`会保证遍历时按照插入顺序【默认】或者访问顺序取出

首元素可用 **map.entrySet().iterator().next().getKey()** 得到

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202304071838540.png" alt="image-20230407183829471" style="zoom: 80%;" />

```java
// 在此映射中关联指定值与指定键
put(key,value)			
// 从此映射中移除指定键的映射关系（如果存在)
remove(key)			
// 清空
clear() 				
// 如果包含指定键，返回true
containsKey(key)		
// 如果包含指定值，返回true
containsValue(value)
// 返回指定键的值，如果不存在返回null
get(key)	
// 返回指定键的值，如果不存在返回defaultValue
getOrDefault(key, defaultValue)
// 返回此映射中当前第一个（最低）键
firstKey()				
// 返回映射中当前最后一个（最高）键
lastKey()				
// 返回大于等于给定键的最小键；如果不存在这样的键，则返回 null
ceilingKey(key)		
// 返回小于等于给定键的最大键；如果不存在这样的键，则返回 null
floorKey(key)	
// 是否空/map大小    
isEmpty()/size()
```

## 遍历方式 ##

```java
TreeMap<Integer, Integer> map = new TreeMap<Integer,Integer>();
map.put(1, 2);
for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
    System.out.println("关键字："+entry.getKey());
    System.out.println("值："+entry.getValue());
}
```

# 5. 大/小根堆PriorityQueue #

> 因为其本质是根堆而不是队列，因此遍历输出得到的元素无序，**依次弹出根顶元素才有序**

```java
// 清空
clear()				
// 如果包含指定元素返回true
contains(ele) 		
// 将指定元素插入此优先队列
offer(ele) 			
// 获取根堆顶元素
peek() 				
// 获取并移除根堆顶元素
poll() 				
// 移除指定元素
remove(ele) 
// 返回元素个数
size() 					
```

## 大小根堆选择 ##

```java
// 默认为小根堆，大根堆需要重新方法
PriorityQueue<Integer> queue = new PriorityQueue<Integer>(new Comparator<Integer>() {
			@Override
			public int compare(Integer o1, Integer o2) {
                 // 后面比前面大就交换，即大的在前面，就是大根堆
				return o2-o1; 
			}
		});
```

# 6. 字符串 #

> `String`用来处理最终结果，`StringBuilder`用来拼接

## 6.1 String ##

```java
// 返回此字符串的长度
length()			
// 判空，当长度为0时返回true
isEmpty()
// 除去任何前导和尾随空格，如果该字符串没有前导或尾随的空格，则返回值为该字符串本身
trim()		
// 返回索引i处的字符，也可以通过toCharArray()转变成数组再遍历
charAt(int i)	
// 按字典顺序比较两个字符串
compareTo(String anotherString)			
// 按字典顺序且不区分大小写比较两个字符串
compareToIgnoreCase(String anotherString)
// 判断两个字符串是否相等，相等返回true否则返回false
equals(String anotherString)
// 同上，不区分大小写
equalsIgnoreCase(String str)		
// 检查是否以某一前缀开始
startsWith(String prefix)		
// 根据指定字符串拆分
split(String regex) 			
// 返回从begin开始到end-1结束的子串
substring(int beginIndex, int end)	
// 将此字符串中的所有字母都换为大写
toUpperCase() 	
// 将此字符串中的所有字母都换为小写
toLowerCase()	

// lastIndexOf有类似用法👇
// 返回指定字符在此字符串中第一次出现的索引
indexOf(char ch)			
// 同上，从指定索引开始搜索
indexOf(char ch, int fromindex) 	
// 返回子串在此字符串中第一次出现的索引
indexOf(String str)			
// 同上，从指定索引开始搜索
indexOf(String str, int fromindex)	
// 用s2替换目标字符串中出现的所有s1
replaceAll(String s1,String s2)	
// 用s2替换目标字符串中出现的第一个s1
replaceFirst(String s1,String s2)	
```

```java
// 类方法【即String.xxx】
// 返回 char数组的字符串表示形式
valueOf(char[] data)				
// 返回 char 数组参数的特定子数组的字符串表示形式
valueOf(char[] data,int offset,int count)
// 返回参数【int,boolean...】的字符串表示形式
valueOf(ele)								
```

## 6.2 StringBuilder ##

> 相较于`String`来说，处理速度更快，所以处理字符串的时候一般使用`StringBuilder`，最后再通过`toString()`方法转为字符串

```java
// 构建一个值为str的可变字符串【也可传空参数】
StringBuilder(String str)
// 返回索引i位置的字符
charAt(int i)
// 返回此字符串的长度
length()
// 在此字符串追加str【参数为StringBuilder也可以】
append(String str)
// 在index处插入字符数组c【c也可以是单个字符或者其他类型】
insert(int index, char[] c)	
// 将char的子数组【下标offset开始，长度len】追加到此字符串
append(char[] str, int offset, int len)	
// 移除此序列从start到end-1的字符串
delete(int start, int end)	
// 移除指定索引上的字符
deleteCharAt(int index)	
// 将指定索引处的字符替换为ch
setCharAt(int index, char ch)	
// 将此字符串反转
reverse()			
// 返回此字符串从start开始至length-1结束的String
substring(int start)		
// 返回此字符串从start开始至end-1结束的String
substring(int start, int end)	
// 返回此序列中的String表示形式
toString()		

// lastIndexOf有类似用法👇
// 返回子字符串第一次出现的索引
indexOf(String str)		
// 同上，从指定位置查找
indexOf(String str, int fromIndex)
```

# 7. 工具类 #

## Arrays ##

```java
// 将arr数组元素变为字符串，一般用于输出看看数组情况，省去遍历的繁琐操作
toString(arr)  		
// arr数组排序，可以传入匿名类Comparator自定义排序方式
sort(arr,new Comparator<T>(){}) 	
// arr数组二分查找(需要排好序)元素ele，返回目标值索引，找不到返回-1			
binarySearch(arr,ele) 	
// 复制arr数组[from,to)位置元素，返回复制好的数组副本
copyOfRange(arr,from,to)
// 使用ele元素将数组填充
fill(arr,ele) 		
// 比较两个数组元素的内容是否完全一致
equals(arr1,arr2) 				
```

## Collections ##

```java
// 仅List可用
// 反转List中的元素
reverse(list) 	
// 按照小到大对链表进行排序【默认】，也可以实现Compartor接口自定义排序
sort(list,new Cpmparator<T>(){}) 
// 将list中的i处元素和j处元素进行交换
swap(list,i,j) 
// list2拷贝到list1，要确保list1有足够空间
copy(list1,list2) 	
// 将list中的A替换成B
replaceAll(list,A,B)	
    
// List和Set都可用
// 返回最大、最小元素
max(list)/min(list) 
// ele出现次数
frequency(list,ele) 
```

## Integer ##

```java
// Boolean，Double等都有类似将字符串转换的方法👇
// 将字符串参数解析为带符号的十进制整数
parseInt(String s) 	
// 将字符串参数解析为第二个参数指定的基数中的有符号正整数
// radix参数不填则默认以十进制数进行解析
parseInt(String s, int radix)  
// 将i转为k进制真值【有正负号】
toString(int i，k) 
```

# 8. 日期Calendar #

> 通过设置对象内部字段再通过`get`方法获得数值

+ `YEAR `：指示年份

+ `MONTH`：指示月份

  > ==***MONTH***字段是从`0`月开始计数的，所以`12`月对应的值是`11`==

+ `DAY_OF_MONTH`：指示一个月中的某天。

+ `DAY_OF_WEEK`：指示一个星期中的某天。

+ `DAY_OF_YEAR`：指示当前年中的天数。

+ `DAY_OF_WEEK_IN_MONTH`：指示当前月中的第几个星期。

+ `HOUR`：指示当天中的某小时

+ `MINUTE`：指示当前小时中的某分钟

+ `SECOND`：指示当前分钟中的某秒

```java
// 得到Calendar对象
Calendar calendar = Calendar.getInstance();	
// 第一个参数是日期字段，诸如YEAR、MONTH等将给定的日历字段设置为给定值
set(int field, int value)
// 设置日历字段年月日的值
set(int year, int month, int day)
// 获取给定字段的值
get(int field);		
// 以毫秒为单位返回日历时间值
getTimeInMillis() 		
// 根据日历的规则，为给定的日历字段添加或减去指定的时间量
add(int field, int amount)
```

# 9. 数学工具Math #

```java
// a,b的最值
min(a,b)/max(a,b)
// 返回正确舍入的 double 值的正平方根
sqrt(double a)		
// 绝对值
abs(a)		
// a的b次方，返回一个double类型的数。
pow(double a, double b) 
// 向上取整
ceil(double x)		
// 向下取整
floor(double x)	
// 四舍五入取整
round(double x)	
// 生成一个[0,1)之间的double类型的伪随机数
random()				
// tan，cos与sin类似
// acos(-1)=π
// 正弦值
sin(double a) 
// 反正弦值
asin(double a) 			
```

# 10. 大数类 #

```java
// 传入字符串参数直接创建，可以使用=进行同类型赋值
BigInteger a = new BigInteger("123456789101112131415");
BigDecimal c = new BigDecimal("123456.123456");
// 以二进制解析"111110"，变为10进制赋值给d
BigInteger d = new BigInteger("111110", 2);	
// 把a转化为16进制的字符串输出
System.out.println(a.toString(16));			
// a对象值+b对象值并将结果返回
a.add(b)
// 减法，同上👆
a.subtract(b) 
// 乘法，同上
a.multiply(b) 
// 除法，同上
a.divide(b)   
// 取余，同上
a.mod(b)
// 最大公因数，同上
a.gcd(b)	
// 最值，同上
a.max(b)/a.min(b)	
// a的b次方
a.pow(b) 
// 比较大小，a大返回1
a.compareTo(b) 
// 把BigInteger 转化为 BigDecimal
toBigDecimal() 
// 把BigDecimal 转化为 BigInteger
toBigInteger() 
```

> `BigDecimal`在乘除时可以指定结果舍入方式

```java
// 只有乘除法才有舍入这个说法
// 向零舍入。 即1.55 变为 1.5 , -1.55 变为-1.5
ROUND_DOWN 
// 向正无穷舍入. 即 1.55 变为 1.6 , -1.55 变为 -1.5
ROUND_CEILING
// 向负无穷舍入. 即 1.55 变为 1.5 , -1.55 变为 -1.6
ROUND_FLOOR
// 四舍五入 即1.55 变为1.6, -1.55变为-1.6
ROUND_HALF_UP 
// 五舍六入 即 1.55 变为 1.5, -1.5变为-1.5
ROUND_HALF_DOWN 
    
// a除b结果四舍五入并保留两位小数返回给c
BigDecimal c = a.divide(b, 2, BigDecimal.ROUND_HALF_UP);
// a保留两位小数并且向0舍入
a = a.setScale(2, BigDecimal.ROUND_DOWN);
```

# 11. 输入输出 #

## Scanner

```java
Scanner scanner = new Scanner(System.in);
// 以空格和换行符作为分隔符接收字符串，即跳过空格和换行符接收
scanner.next();
// 以换行符作为分隔符，返回换行符之前的字符串
scanner.nextLine();
```

## BufferedReader ##

```java
// 初始化一个缓冲输入流
BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
// 读取一个文本行，一般配合split将读取字符串分割，用Integer将其转型
readLine();	
```

## BufferedWriter ##

```java
// 初始化一个缓冲输出流
BufferedWriter out = new BufferedWriter(new OutputStreamWriter(System.out));
// 将a送入输出流中，一般将需要的输出一并放到输出流中，此时并未输出
write(a);
// 刷新输出流，将此时输出流的东西输出
flush();	
```

# 12. 常用封装算法

## 12.1 质数判断

**大于等于`5`的质数一定和`6`的*倍数*相邻，1不是质数**

```java
// 判断素数的方法，已知1不是素数，在主函数中去除1即可
public static boolean isPrimeNumber(int n) {
		if (n == 2 || n == 3) {
			return true;
		}
		if ((n - 1) % 6 == 0 || (n + 1) % 6 == 0) {
			// 当前可能是素数
			for (int i = 2; i <= Math.sqrt(n); i++) {
				if (n % i == 0) {
					return false;
				}
			}
			return true;
		} else {
			return false;
		}
	}
```

## 12.2 最大公因数/最小公倍数

```java
// 最大公约数，此法原理为欧几里得算法，可以理解为是发现的一个规律，不必深究
int gcd(int a, int b) {
		return b == 0 ? a : gcd(b, a % b);
}

// 最小公倍数 = 两数之积 / 两数最大公约数
int lcm(int a, int b) {
		return a * b / gcd(a, b);
}
```

## 12.3 快速幂

**快速幂的原理是通过将幂转换为二进制形式进行分析从而减少相乘次数**
$$
正常进行计算时：3^7=3*3*3*3*3*3*3\\
快速幂则是这样处理，首先7=(111)_2，所以7=1+2+4\\
因此3^7=3^1*3^2*3^4(2^0=1,2^1=2,2^2=4)\\
这样计算次数就从原来的7次降低为3次，问题的关键便是得到每次乘的数字\\
由于我们是从二进制角度进行分析，因此位运算往往能派上用场，这就是快速幂整体思想
$$

```java
	// 快速幂
	static int qmi(int a, int b) {
        // 保存结果
		int res = 1;	
		while (b != 0) {
			if ((b & 1) == 1) {	
                // 当前最右边二进制位上有值时，说明当前a值是我们所需要的
				res = res * a;
			}
            // b每次处理完一位后往右移
			b = b >> 1;	
            // b完成一次位移后，a的次方数应当*2才能满足下一次乘数的要求
			a = a * a;	
		}
		return res;
	}
```

**`qmi(3,7)`计算过程如下(求 $3^7$ )：**
$$
1 .res=1，a=3,b=7=(111)_2，此时b \& 1=(111)_2\&(001)_2=1\\	可知当b二进制形式的最后一位不为0时，b\&1==1
\\
2. res=a*res=3*1=3=3^1；b>>1=(011)_2,a=3*3=3^2\\
3. 再次进入循环，res=res*a=3^1*3^2,b>>1=(001)_2,a=a*a=3^2*3^2=3^4\\
4. res=3^1*3^2*3^4,b>>1=(000)_2,a=3^4*3^4=3^8\\
5. 由于此时b==0，所以跳出循环，返回结果res=3^7，即答案
$$

## 12.3 快速幂求模

> 详细原理可看[快速幂取模](https://dengbocong.blog.csdn.net/article/details/77646508)

```java
// 原理：(a*c)%mode==((a%mode)*(c%mode))%mode
// 此函数是求(a^b)%mode
	static long Mode(long a, long b, long mode) {
		long sum = 1;
		a = a % mode;
		while (b > 0) {
			if ((b & 1) == 1) { 
				sum = (sum * a) % mode;
			}
			b = b >> 1;
			a = (a * a) % mode;
		}
		return sum;
	}
```

