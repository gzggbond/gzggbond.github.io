---
title: Java基础
date: 2024-10-08 05:27:50
excerpt: 本文对于Java的基本用法做了系统性总结，持续更新
tags:
  - 编程语言
categories:
  - [计算机基础,编程语言]
---

**B站课程链接**：`https://www.bilibili.com/video/BV1fh411y7R8?spm_id_from=333.999.0.0`

# 1. 文档注释

> 用于对Java方法的注释，可据此生成参考文档。使用方式如下

```java
/**
 * @author 捉妖龙
 * @version 1.0
 */
```

|          标签          |                             说明                             |              标签类型               |
| :--------------------: | :----------------------------------------------------------: | :---------------------------------: |
|      @author 作者      |                           作者标识                           |            包、类、接口             |
|    @version 版本号     |                            版本号                            |            包、类、接口             |
|   @param 参数名 描述   |     方法的入参名及描述信息，如入参有特别要求，可在此注释     |           构造函数、方法            |
|      @return 描述      |                      对函数返回值的注释                      |                方法                 |
|  @deprecated 过期文本  | 标识随着程序版本的提升，当前API已经过期，仅为了保证兼容性依然存在，以此告之开发者不应再用这个API | 包、类、接口、值域、构造函数、方法  |
|    @throws 异常类名    |                 构造函数或方法所会抛出的异常                 |           构造函数、 方法           |
|  @exception 异常类名   |                          同@throws                           |           构造函数、 方法           |
|       @see 引用        |               查看相关内容，如类、方法、变量等               | 包、类、接口、值域、构造函数、方法  |
|    @since 描述文本     |              API在什么程序的什么版本后开发支持               | 包、类、接口、值域、构造函数、 方法 |
| {@link包.类#成员 标签} |               链接到某个特定的成员对应的文档中               | 包、类、接口、值域、构造函数、 方法 |
|        {@value}        | 当对常量进行注释时，如果想将其值包含在文档中，则通过该标签来引用常量的值 |              静态值域               |

### Eclipse生成参考文档 ###

![1](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171912861.png)

![2](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171912638.png)

# 2. 路径 #

> 相对路径：从当前目录开始定位，形成的一个路径
>
> 绝对路径：从顶级目录(磁盘)开始定位，形成的路径

```java
..\相对路径     // 使用..的形式到达当前目录的上一级
磁盘:\路径\....  // 绝对路径的写法   
```

# 3. DOS命令 #

```java 
dir 路径			//查看指定目录下有什么内容，若不填路径则查看当前定位到的目录有什么内容
cd D 磁盘号:		//切换到指定磁盘
cd 路径        	//切换到指定路径(可相对可绝对)
cd ..         	//切换到上一级
cd \         	//切换到当前目录根目录
    
tree 路径		//查看路径下的所有子目录
cls				//清屏
exit			//退出命令提示符
    
md 文件夹名     //在当前目录创建文件夹(可一次创建多个，文件夹名空格隔开)
rd 文件夹名		//同理，删除文件夹
echo ok > example.txt  //创建example.txt并且其中内容为“ok”
type nul > example.txt  //创建example.txt并且其中无内容
del 文件名   //删除文件
copy exa.txt e:\exa2.txt  //将当前目录下的exa.txt拷贝到e盘下的exa2.txt(若无则创建) 
move exa.txt e:\exa.txt //将当前目录下的exa.txt移动到e盘下的exa.txt(若无则创建)
    
java -jar 文件名.jar  //运行jar文件
```

```java
netstat -an //查看当前主机网络情况，包括端口监听和网络连接情况
netstat -an | more //分页显示，空格查看下一页，回车查看下一个
netstat -anb //显示各个端口是被哪些程序监听，需要管理员权限才能使用
```

# 4. 整型和浮点型 #

### 整型 ###

|       类型        | 占用存储空间 | 范围                                           |
| :---------------: | :----------: | :--------------------------------------------- |
|  byte[**字节**]   |    1字节     | -128~127                                       |
| short[**短整型**] |    2字节     | $-(2^{15})到2^{15}-1\\-32768到32767$           |
|   int[**整型**]   |    4字节     | $-(2^{31})到2^{31}-1\\-2147483648到2147482647$ |
| long[**长整型**]  |    8字节     | $-(2^{63})到2^{63}-1$                          |

**==java中的整型常量默认为int型，声明long型常量需要加`l`或`L`==**

```java
long a=2L;
// 虽然10是int型，但是由于其在byte的范围内，所以这样没有问题
byte b=10; 
int c=10;
// 这样是错的，因为c划分了空间，byte比较小，无法赋值
b=c;  
```

### 浮点型 ###

| 类型   | 占用存储空间 |
| ------ | ------------ |
| float  | 4字节        |
| double | 8字节        |

**==java中的浮点型常量默认为double型，声明float型常量需要加`f`或`F`==**

```java
float n=6.5F;
// 省略小数点前的0，也表示0.512
double b=.512 
// 科学计数法形式
5.12e2  // 5.12*10的2次方
5.12e-2  // 5.12*10的-2次方
```

**在浮点数中，乘除法得出来的结果是一个近似数**

```java
// 结果不为0.4，而是0.39999999999999997
double a=2.4/6;      
// 因此，若要比较两个浮点数经过乘除运算后是否相等，应该采用绝对值求精度的方式
double b=0.4;
if(Math.abs(a-b)<0.01)  //在此例中，人为规定当误差小于0.01就认为它们相等
```

# 5. 类型转换 #

### 自动类型转换 ###

**占据空间少的类型可以向上兼容(boolean型不参与自动转换)**

```java
byte a=1;
// 这是对的，因为byte空间少于int
int b=a; 
// 这也是对的，char本质是整型，可以兼容
char c=b; 
// 这是错的，因为java规定(byte,short)不能自动转换为char型
a=c; 
// byte,short,char三者之间可以计算，在计算时候首先自动转换为int类型
```

### 强制类型转换 ###

```java
// 当进行数据从大-->小，需要用到强制类型转换
int n=(int) 5.2;
// 强转符号只针对最近的操作数有效
// 先对5.5进行强转再运算，可以用括号的形式对指定数据强转
int n=(int)5.5*2.1; 
```

### 基本数据类型转字符串 ###

```java
// 基本类型转字符串直接【基本类型值+""】即可
boolean flag=true;
// 将true转换成字符串并赋值给s
String s=flag+""; 
// 字符串转基本类型则是调用类方法[parseXXX]
// 将"true"转换成布尔型并赋值
boolean flag2=Boolean.parseBoolean("true"); 
// 在转换过程中要确保传进来的字符串可以转换成有效数据，否则会抛出异常
```

# 6. 逻辑运算符 #

+ 短路与 &&：若第一个条件为 false ，第二个条件不会执行判断，最终结果为 false   

  逻辑与 &：不管第一个条件是什么，后边的都会判断，最终结果也为 false

+ 短路或 ||：若第一个条件为 true，第二个条件不会执行判断，最终结果为 true  

  逻辑或 |：不管第一个条件是什么，后边的都会判断，最终结果也为 true

+ 逻辑异或`^`：A`^`B，当A和B取值不同时结果为 true，反之为 false

# 7. 输入 #

```java
// Scanner类实现输入
Scanner scanner = new Scanner(System.in);
// next()通过空格将输入划分成几段字符串，读取时只能读取到光标到空格前的内容
// 下次next()时，光标会跳过分隔符和换行符直达下一个字符串的开头，读取到这个新字符串
// 如果跳过分隔符之后没有字符串了，那就需要重新读取输入才能获取到

// 获取输入中第一个字符串(空格为分隔符)
String s=scanner.next(); 
// 获取输入流中的数字字符串并转换成整型，光标原理与next()类似
int a=scanner.nextInt();

// 此方法也是获取字符串，不过只有遇到回车符才终止，可以读取空格
// 当nextLine()放在其他next方法后使用时需要格外注意
// 因为其他方法会跳过空格和回车符再开始接收数据，而nextLine()方法不会跳过
// 因此很有可能一调用就遇到回车符号，这时往往需要多使用一次nextLine()让其"吃掉"回车符\r
String s2=scanner.nextLine(); 

// 若要一次接收大量数据，可以考虑使用BufferedReader，效率更高
BufferedReader reader=new BufferedReader(new InputStreamReader(System.in));
// 读取一整行数据，可配合字符串的split(" ")将其分割
reader.readLine(); 
```

# 8. 位运算 #

+ ~ 按位取反(单目)	

  & 按位与	

  | 按位或	

  ^ 按位异或

+ `>>`算术右移：低位溢出【消失】，符号位不变，用符号位补溢出的高位【符号位后的数字集体右移，多出来的空补符号位数字】

  > `>>>`无符号右移：低位溢出，高位补0

  ```java
  int a = 8;
  // 输出结果为2
  System.out.println(a>>2);
  ```

+ `<<`算术左移：符号位不变，低位补0【与右移同理】

# 9. IDEA使用 #

[IDEA使用](F:\笔记\IDEA快捷键)

# 10. 访问修饰符 #

> Java提供四种访问控制修饰符号，用于**控制方法和属性**(成员变量)的访问权限(范围)

+ **public**：对外公开
+ **protected**：对子类和同一个包中的类公开
+ **没有修饰符**：向同一个包的类公开
+ **private**：只有类本身可以访问，不对外公开

| 访问级别 | 访问控制修饰符 | 本类 | 同包 | 子类 | 不同包 |
| -------- | -------------- | ---- | ---- | ---- | ------ |
| 公开     | public         | √    | √    | √    | √      |
| 受保护   | protected      | √    | √    | √    | ×      |
| 默认     | 没有修饰符     | √    | √    | ×    | ×      |
| 私有     | private        | √    | ×    | ×    | ×      |

+ 只有`public`可以修饰类【或者不写修饰符】
+ **子类与父类不在同一个包中时**，子类只继承`public和protected`的成员变量和方法

# 11. 继承 #

> 子类继承父类（除私有外）所有的属性和方法【若在不同包中则只继承公有和保护属性方法】

+ **父类的私有属性和方法在子类中无法被访问，需要通过父类提供公共的方法去访问**

  ```java
  public class A {
      private int a = 10;
      public void sayA(){
          System.out.println(this.a);
      }
  }
  // B是A的子类
  class B extends A { 
      public void test(){
          // 这样直接输出A.a属性时错误的，因为其是私有属性
          System.out.println(a); 
          // 这样输出是对的，因为sayA是一个公共方法，方法内实现输出
          sayA(); 
      }
  }
  ```

+ **子类执行构造函数前会先调用父类构造器，完成父类初始化**【默认执行无参构造器，当父类没有无参构造器时，子类必须使用**`super`关键字（放在构造器第一行）**指定父类构造函数，否则报错】

+ `this()`意思为调用本类的无参构造器（也可以填参数），其也需要放在构造函数第一行，因此与`super()`不能共存

+ `Java`的所有类都是`Object`类的子类

+ **子类最多只能直接继承一个父类【即间接继承是允许的】**

## super关键字 ##

> `super`代表父类的引用，用于访问父类的属性、方法、构造器

```java
// 访问父类属性，但不能访问父类的private属性
super.属性名  
// 同理不能访问父类private方法
super.方法名(参数) 
// 构造函数，只能放子类构造器第一句
super()
```

# 12. 方法重写 #

> 即子类的方法名、参数与父类的一致，叫方法的重写

+ **子类方法的返回类型**必须**等于父类方法返回类型**或者是其子类

  ```java
  public class A{
      public Object say(){
      }
  }
  public class B extends A{
      // 这也是方法的重写，因为String是Object的子类 
      public String say(){  
      }
  }
  ```

+ **子类方法不能缩小父类的访问权限**

  ```java
  //如上例子
  public class B extends A{
      // 这是错误的方法重写，因为protected范围小于public
      protected String say(){  
      }
  }
  ```


# 13. 多态 #

### 向上转型 ###

> 父类为壳，子类为内容

+ **一个对象的编译类型和运行类型可以不一致**

  ```java
  // 父类引用可以指向子类对象
  // animal编译类型是Animal，运行类型是Dog
  Animal animal = new Dog(); 
  // animal运行类型变成了Cat，编译类型依然是Animal
  animal=new Cat();
  ```

+ **编译类型**在定义对象时就确定了，**不能改变**

  ```java
  // 定义了animal的编译类型就是Animal，不能改变
  Animal animal; 
  ```

+ **运行类型是可以变化的**

  ```java
  // animal可以指向Dog，也可以指向Cat
  Animal animal=new Dog(); 
  ```

+ **可以调用父类的所有成员【遵守访问权限】**

  因为当子类`new`出来后，势必会调用父类构造器，即父类被创建了，自然可以调用父类所有成员。但是本质上还是子类，所以需要遵守访问权限

+ **不能调用子类的特有成员**

  创建对象前需要进行编译，编译的类型是父类，所以父类并不认识子类的方法；而共有的方法父类是认识的，编译可以通过，编译结束后开始进入执行阶段，因此调用共有方法时会最底层开始找起，即子类

### 向下转型 ###

> 将**父类引用**强制转换为子类引用

+ 语法：**子类类型 引用名=(子类类型) 父类引用**

  ```java
  // 成功使用的前提是，父类引用animal必须先前指向了子类对象
  Dog dog=(Dog) animal; 
  ```

+ **只能强转父类引用，不能强转父类对象**

  ```java
  // 这是错误的，不能强转父类对象
  Dog dog=(Dog) new Animal; 
  ```

+ **要求父类的引用必须指向的是当前目标类型的对象**

+ **当向下转型后，可以调用子类类型中所有成员**

+ **属性没有重写之说，属性值看编译类型**

  ```java
  Animal animal=new Dog();
  // 输出的age是Animal中的age，而不是Dog的age
  System.out.println(animal.age); 
  ```

+ `instanceof`用于判断对象的**运行类型**是否为**XX类型或XX类型的子类型**

  ```java
  // dog是Animal的子类型，输出true
  System.out.println(dog instanceof Animal); 
  ```


### 动态绑定机制 ###

+ 当调用对象方法时，该方法会和该对象的**运行类型**绑定

+ 当调用对象属性时，没有动态绑定机制，哪里声明，哪里使用

  ```java
  class A{
      int i=10;
      public int getI(){
          return i;
      }
      public int sum(){
          return getI()+10;
      }
  }
  class B extends A{
      int i=20;
      public int getI(){
          return i;
      }
  }
  
  A a=new B();
  // 首先去B里找，发现没有sum()方法，因此回到父类找，发现有sum()，进入，执行getI()
  // 根据动态绑定机制，方法执行与运行类型有关，因此又跑到子类去找
  // 发现有getI()，又根据动态绑定机制：属性哪里声明哪里调用，因此返回B中的i=20。回到A后+10，最后输出结果为30
  System.out.println(a.sum());
  ```


# 14. 多态数组 #
 > 即数组的编译类型为父类，执行类型为子类

  ```java
  Person persons=new Person[3];
  persons[0]=new Student();
  persons[1]=new Teacher();
  ```

+ 利用多态数组的形式进行`for`循环可以**调用每个子类中重载的方法**

  ```java
  for(int i=0;i<persons.length;i++){
      // say()是共有的方法
      persons[i].say(); 
  }
  ```

+ 多态数组利用`instanceof`可以**调用各子类的特有方法**

  ```java
  for(int i=0;i<persons.length;i++){
      if(persons[i] instanceof Student){
          // 向下转型
          Student stu=(Student) persons[i]; 
          stu.study();
      }else{
          // 向下转型
          Teacher tea=(Teacher) persons[i]; 
          stu.teach();
      }
  }
  ```

### 多态参数 ###

> 参数中是一个父类，传进来的参数可以是子类（同样可以配合`instanceof`）

```java
public void test(Person person){
    // 当Teacher调用这个方法时就是Teacher的say()；
    // Student就是Student的say()
    person.say(); 
}
```

# 15. Object类 #

> `Object`是所有类的父类，所以它的方法所有类都会有

![3](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171912752.png)

### ①==与equals()的区别 ###

**1. ==是一个比较运算符**

+ 既可以判断基本类型，又可以判断引用类型
+ 如果判断**基本类型**，判断的是**值是否相等**
+ 如果判断**引用类型**，判断的是地址是否相等，即**判断是不是同一个对象**

**2. equals()是Object的一个方法**

+ 只能判断引用类型，即**判断地址是否相等【其他类多重写此方法，判断内容是否相同】**

+ **String就对equals()进行了重写**，源码如下

  ```java
  public boolean equals(Object anObject) {
          if (this == anObject) { //是同一个对象，返回true
              return true;
          }
          if (anObject instanceof String) { //是一个String对象
              String anotherString = (String)anObject; //向下转型取得数据
              int n = value.length;
              if (n == anotherString.value.length) {
                  char v1[] = value;
                  char v2[] = anotherString.value;
                  int i = 0;
                  while (n-- != 0) { //逐个比较字符，若有不同则返回false
                      if (v1[i] != v2[i])
                          return false;
                      i++;
                  }
                  return true; //比较字符串全部相同则返回true
              }
          }
          return false; //不是字符串返回false
      }
  ```

### ②hashCode() ###

+ 提高具有哈希结构的容器效率
+ 两个引用，如果指向的是**同一个对象，则哈希值一定相同；反之不同**
+ 哈希值主要根据地址号得来，但是不能完全将哈希值等价于地址
+ `hashCode()`如果需要的话，也会重写
+ ==**类属性值发送改变后哈希值也会发生改变**==

### ③toString() ###

+ **默认返回：全类目(包名+类名)+@+哈希值16进制【源码如下】**

  ```java
  public String toString() {
      	// getClass().getName()当前类的全类名
          return getClass().getName() + "@" + Integer.toHexString(hashCode());
      }
  ```

+ **子类往往重写toString()，用于输出对象属性**

  ```java
  class monster{
      String name;
      int age;
  	//快捷键Ctrl+Enter
      @Override
      public String toString() {
          return "monster{" +
                  "name='" + name + '\'' +
                  ", age=" + age +
                  '}';
      }
  }
  ```

+ **直接输出一个对象时，toString()会被默认调用**

### ④finalize() ###

+ 对象被回收时，系统自动调用该对象的`finalize`方法。子类可以重写该方法做一些**释放资源**的操作

+ **什么时候被回收？**当某个**对象没有任何引用时**，则`jvm`就认为这个对象是一个垃圾对象，就会使用垃圾回收机制来销毁对象，在销毁该对象前，会先调用`finalize`方法。如果程序员没有重写`finalize`，那么会调用`Object`类的`finalize()`，即默认处理

  > 但是并不是对象没有引用就马上启动垃圾回收机制，而是有自己的算法。如下↓

+ 垃圾回收机制的调用，是由系统来决定，也**可以通过System.gc()主动触发垃圾回收机制**

# 16. 断点调试debug #

> 要记得打断点！！！！

```java
// 跳入方法内
F7  
// 逐行执行代码
F8  
// 跳出方法
shift+F8  
// 执行到下一个断点
F9   
// 强制进入库函数源码
Alt+Shift+F7  
```

+ 在`Debug`过程中也是可以放断点的，**F9可以直接跳到下个断点**

# 17. 类(静态)变量 #

> 由`static`修饰的变量（静态变量）**是所有类成员共享的**。在类加载的时候就生成了。

```java
//类变量的定义语法
class A{
    访问修饰符 static 数据类型 变量名
    public static String name="1"; //推荐
    static public int num=1;
}
// 访问类变量
类名.类变量名 // 推荐
对象名.类变量名
```

+ **访问修饰符的访问权限**和范围与普通属性一致

# 18. 类(静态)方法 #

> 由`static`修饰的方法（静态方法）**是所有类成员共享的**。在类加载的时候就生成了。

```java
// 类方法语法格式
// static和访问修饰符可换位
访问修饰符 static 数据返回类型 方法名(){} 
public static int sum(){} //推荐
// 访问类方法
类名.类方法名 //推荐
对象名.类方法名
```

+ 当方法中不涉及到任何与对象相关的成员，则可以将方法设计成静态方法，提高效率
+ 类方法不允许使用和类相关的关键字，如`super`和`this`
+ **类方法只能访问静态成员**

# 19. main方法 #

+ `main`方法是虚拟机调用的

+ `Java`虚拟机需要调用类的`main()`方法，所以该方法的访问**权限必须是public【虚拟机和文件不在同个包中】**

+ `Java`虚拟机在执行`main()`方法时**不必创建对象，所以该方法必须是static**

+ 该方法接收`String`类型的数组参数，该**数组在保存执行java命令时传递给所运行的类的参数**

  ```java
  public class  A
  {
  	public static void main(String[] args) 
  	{
  		for(int i=0;i<args.length;i++){
  		System.out.println("第"+(i+1)+"个参数为："+args[i]);
  		}
  	}
  }
  ```

  ![4](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171912517.png)

+ 因为`main()`是一个静态方法，因此**在方法内可以调用静态成员**，非静态成员需要创建类后调用

# 20. 代码块 #

> 构造函数的补充，优先于构造函数执行。当构造函数有多个重复部分时，可以用代码块使代码变得整洁

+ **使用如下↓**

```java
// 语法：用{}将内容包含
class A{
    int a=10;
    // 可以加上static修饰符，此时是静态代码块
    { 		
        System.out.println("我是代码块！");
    }
    public A(){
        System.out.println("A()被调用");
    }
    public A(int num){
        System.out.println("A(num)被调用");
    }
}
```

+ **普通代码块每创建一个对象就执行一次；静态代码块只会在==类加载时==执行一次**
  + **类什么时候被加载？**
    1. **创建对象实例**的时候【new】
    2. **创建子类对象实例，父类也会被加载**
    3. **使用类的静态成员属性时**
+ 普通代码块在创建对象实例时，会被隐式调用
+ 若有静态代码块和普通代码块，则**静态代码块内容会优先输出**
  + **若静态代码块放在静态属性初始化前边，则先输出静态代码块内容，反之先执行静态属性初始化**
  + **若普通代码块放在普通属性初始化前边，则先输出普通代码块内容，反之先执行普通属性初始化**
+ 当子类对象创建时，父类与子类的代码块及构造函数按如下顺序调用
  1. **父类的静态**代码块和静态属性(优先级一样，按定义顺序执行)
  2. **子类的静态**代码块和静态属性(优先级一样，按定义顺序执行)
  3. **父类的普通代码块和普通属性初始化(优先级一样，按定义顺序执行)**
  4. **父类的构造方法**
  5. **子类的普通代码块和普通属性(优先级一样，按定义顺序执行)**
  6. **子类的构造方法**
+ **静态代码块只能直接调用静态成员，普通代码块可以调用任意成员**

# 21. final关键字 #

> final可以修饰类、属性、方法和局部变量。final修饰的属性叫常量

+ 当**不希望类被继承时**，可以用final修饰类
+ 当**不希望父类的某个方法被子类覆盖/重写(override)时**，可以用final关键字
+ 当**不希望类的某个属性值被修改**，可以用final修饰
+ 当**不希望某个局部变量被修改**，可以使用final修饰

```java
// 语法
访问修饰符 final class 类名
访问修饰符 final 返回类型 方法名
访问修饰符 final 参数类型 参数名
```

+ **final修饰的属性一定要初始化**

  + final修饰**普通参数**的初始化**可以在定义的时初始化，可以在代码块初始化，也可以在构造器中初始化**

  + final修饰**静态参数**的初始化可以在定义的时初始化，也可以在静态代码块中初始化，**不能在构造器中初始化**

+ 如果类不是final类，但是有final方法，则**该方法虽然不能重写，但是可以被继承**

+ 如果一个类已经是final类，就没有必要再将里边的方法修饰成final方法【画蛇添足】

+ **final和static往往搭配使用，效率更高**。因为底层编译器做了优化处理，不会导致类加载

# 22. 抽象 #

> 父类方法具有不确定性时考虑将父类设置为抽象类
>抽象方法就是没有实现的方法，抽象方法必须封装在抽象类中

+ **用`abstract`关键字修饰一个类时，这个类就叫抽象类**

  ```java
  // 语法：访问修饰符 abstract 类名{}
  abstract class A{
      // 访问修饰符 abstract 返回类型 方法名(参数列表)
      // 不能有方法体
      public abstract void test(); 
  }
  ```

+ **抽象类不能被实例化【即不能被new】**

+ **抽象类不一定要包含abstract方法**

+ **abstract只能修饰类和方法，不能修饰属性**

+ **==抽象类本质还是类，因此类可以有的它都可以有==**，除了抽象方法不能有方法体

+ 如果**一个类继承了抽象类，则它必须实现抽象类的所有抽象方法**，除非它自己也声明为abstract类

# 23. 接口 #

> 接口就是给出一些没有实现的方法，封装到一起，到某个类要使用时，根据具体情况把这些方法写出来
>当子类继承了父类，就自动拥有了父类的功能；如果子类需要扩展功能，可以通过实现接口的方式进行扩展

```java
// 语法
interface 接口名{
    // 属性
    // 方法
}
class 类名 implements 接口{
    自己属性;
    自己方法;
    必须实现的接口的抽象方法;
}
```

+ **接口中的方法默认是抽象方法，没有方法体且修饰符默认为public**

  + 可以加`static`成为静态方法，此时需要自行实现
  + 普通方法可以有方法体，不过需要在最前面**加`default`关键字**

+ **通过传入接口参数实现统一调用**

  ```java
  public class SmallChangeSys {
      public static void main(String[] args) {
          // A实现了接口
          method.test(new A()); 
      }
  }
  interface myInterface{
      public void test1();
      public void test2();
  }
  class A implements myInterface{
      @Override
      public void test1() {
          System.out.println("我是A的方法1");
      }
      @Override
      public void test2() {
          System.out.println("我是A的方法2");
      }
  }
  class method {
      public static void test(myInterface face) {
          // 通过接口进行统一调用
          face.test1(); 
          // 参数是实现接口的哪个类就调用对应方法
          face.test2();  
      }
  }
  ```

+ 接口是抽象的概念，自然**不能被实例化**

+ **抽象类可以不实现接口方法**

+ **一个类可以同时继承多个接口，用逗号隔开**

+ 接口中的属性只能是final的，而且是`public static final`，如`int a=1`实际上是`public static final int a=1`【必须初始化】

+ **==接口不能继承类，但是可以继承`extends`多个别的接口==**

+ 接口也支持向上转型

  ```java
  //如下是合法的
  public class SmallChangeSys {
      public static void main(String[] args) {
          MyInterface myInterface = new A();
      }
  }
  class A implements MyInterface { }
  interface MyInterface { }
  ```

+ **接口有多态传递特性**

  ```java
  public class SmallChangeSys {
      public static void main(String[] args) {
          Face1 face1 = new Example();
          // 由于Face1继承了Face2，所以Example也实现了Face2
          Face2 face2 = new Example(); 
          // face2只能调用接口Face2有的方法
          face2.test();
      }
  }
  interface Face1 extends Face2 {
      public void another();
  }
  interface Face2 {
      public void test();
  }
  class Example implements Face1{
      @Override
      public void test() {
          System.out.println("god");
      }
      @Override
      public void another() {
          System.out.println("so god");
      }
  }
  ```

# 24. 内部类 #

> 一个类的内部又完整的嵌套了另一个类结构。**被嵌套的类就称为内部类，嵌套类的类称为外部类**
>
> **内部类的最大特点就是可以直接访问私有属性**，并且可以体现类与类之间的包含关系

```java
// 外部类
class A{ 
    int age=0;
    // 内部类
    class B{ 
        
    }
}
// 外部其他类
class C{} 
```

## 局部内部类 ##

> 局部内部类是定义在外部类的局部位置，比如**方法中，并且有类名**

```java
class A{
    int age=0;
    public void m(){
        // 局部内部类地位是局部变量，因此不能加访问修饰符，但是可以使用final
        // 作用域：仅仅在定义它的方法或代码块中有用
        // 本质还是类，因此也可以被继承(如果没final)
        final class B{ 
            // 可以直接访问外部类的所有成员
            System.out.println("年龄："+age);
        }
        // 拥有内部类的方法若要调用内部类的非静态方法，可以创建对象调用
        B b=new B();
    }
}
```

+ **外部其他类不能访问局部内部类**，因为局部内部类的地位是方法里的一个局部变量

+ **如果外部类和局部内部类的成员重名时，默认遵循就近原则，如果想访问外部类的成员，可以使用【外部类名.this.成员】去访问**

  ```java
  Class A{
      int age=0;
      public void test(){
          class B{
          int age=10;
          public void test2(){
              System.out.println("这样输出age是10"+age);
              // A.this指向调用了test()方法的对象
              System.out.println("这样输出age是0"+A.this.age);
          }
     	  }
      }   
  }
  ```


## 匿名内部类 ##

> 没有显示给出类的名字，其本质上是一个子类，底层实际上是自动分配了类名【格式为：外部类$序号】。

+ 可以**直接访问外部类的所有成员【直接访问】**，包括私有
+ 仅在定义它的方法或者代码块中起作用
+ **本质上是一个子类，因此子类能做的它几乎都可以做【如属性、方法，super的使用等】**
+ **匿名内部类的传入参数会交给父类构造器处理**
+ 如果外部类与匿名内部类的成员重名，遵循就近原则；若想调用外部类的成员则使用【外部类名.this.属性】

### 匿名类实现接口 ###

```java
public class Small {
    public static void main(String[] args) {
        new Small().method();
    }
    public void method(){
        // 匿名内部类的意思是此类在new出来后只使用一次
        // 后面即使再new一个同样的实现方法体，本质上也是不同的类
        // 本质上实现了一个类 class Small$1 implements Face2{}
        // 接口是抽象的，new出来实际是用一个类实现了这个接口并做向上转型
       Face2 face2=new Face2(){ 
            // 实现抽象方法
            @Override
            public void test() { 
                System.out.println("我是匿名内部类");
            }
        };
        // 此匿名内部类的外部类是Small，所以输出Small$1
       System.out.println(face2.getClass()); 
    }
}
interface Face2 {
    public void test();
}
```

### 匿名类实现类 ###

```java
public class SmallChangeSys {
    public static void main(String[] args) {
        new SmallChangeSys().method();
    }
    public void method(){
        // 实际上创建了一个Face2的子类，然后赋值做向上转型
        // class Small$1 extends Face2
        Face2 face2=new Face2(){
            @Override
            public void test() {
                System.out.println("我重写了原来的方法");
            }
        };
        // 子类重写了方法，因此执行的是子类重写的test()
        face2.test();
        System.out.println(face2.getClass());
    }
}
class Face2 {
    public void test(){
        System.out.println("我是原来的方法");
    }
}
```

## 成员内部类 ##

> 定义在外部类成员位置的类。【匿名内部类和局部内部类都是定义在方法中的】

+ 成员内部类的地位是外部类的一个成员，因此**可以任意加访问修饰符**
+ **可以直接访问外部类所有成员；外部类所有成员也可以访问它**

```java
public class Small {
    private int age=10;
    public static void main(String[] args) {
        new Small().method();
    }
    public void method(){
        // 方法中创建成员内部类并调用方法
       new Face2().test(); 
    }
    private class Face2 {
        public void test(){
            // 访问外部类的age
            System.out.println("访问外部类的age"+age); 
        }
    }
}
```

+ **其他外部类访问内部类的方法**

```java
public class Small {
    public static void main(String[] args) {
        //第一种方式
        Face face = new Face();
        Face.Face2 face2=face.new Face2();//Face2是Face的一个成员，可以.出来
        //第二种方式，其实是第一种方式的合并
        Face.Face2 face3=new Face().new Face2();
        //第三种方式，通过外部类的方法返回
        Face.Face2 face4=new Face().get();
    }
}
class Face {
    public void test(){
        System.out.println("访问外部类的age");
    }
    class Face2{ }
    public Face2 get(){
        return new Face2();
    }
}
```

## 静态内部类 ##

> 用static修饰的成员内部类

+ 实际上看作是外部类的静态成员，因此**只能访问外部类的静态成员**
+ 可以添加任意访问修饰符，因为本质是成员

```java
class Face {
    public void test(){
    }
    static private class Face2{ } //是合法的
}
```

+ 外部其他类访问静态内部类

```java
public class Small {
    public static void main(String[] args) {
        // 第一种方式，创建类后直接调用静态成员，括号内为内部类构造函数参数
        Face.Face2 face2=new Face.Face2();
        // 第二种方式，通过方法返回一个内部类实例对象
        Face.Face2 face3 = Face.get();
    }
}
class Face {
    static class Face2{
    }
    public static Face2 get(){
        return new Face2();
    }
}
```

# 25. 枚举 #

> 对于一些对象，我们确认它有固定的几个结果并且不允许它出现其他情况时，就使用枚举类

### 自定义类实现枚举 ###

```java
// 如季节只有春夏秋冬，我们不希望出现其他结果
public class Small {
    public static void main(String[] args) {
        System.out.println(Season.SPRING.getAttr());
    }
}
class Season {
    private String attr;
    private static final String TIME="每个季节3个月";
    // 季节类固定死了就四个
    public static final Season SPRING=new Season("春天"); 
    // 只能通过静态调用
    public static final Season SUMMER=new Season("夏天"); 
    public static final Season AUTUMN=new Season("秋天"); 
    public static final Season WINTER=new Season("冬天");
    // 构造器私有，不允许其他外部类创建
    private Season(String season){ 
        attr=season;
    };
    public String getAttr(){ //外部只能通过这个方法调用参数
        return attr;
    }
}
```

+ 不需要提供`setXxx`方法，**枚举对象值通常为只读**
+ 对枚举对象/属性实现**final+static共同修饰，实现底层优化**
+ 枚举对象名通常**使用全部大写**，常量的命名规范
+ 枚举对象根据需要，**可以有多个属性**

## enum关键字实现枚举 ##

+ 使用**关键字`enum`替代`class`**

+ 直接**使用常量定义代替原先的`static final`类；不同的枚举类间有逗号隔开**

  ```java
  public static final Season SPRING=new Season("春天");
  public static final Season SUMMER=new Season("夏天");
  //变换如下↓
  SPRING("春天"),SUMMER("夏天");
  ```

+ 如果使用`enum`来实现枚举，要求**将定义常量写在类的第一行**

+ 如果调用的是**无参构造器**，则创建常量对象时，**可以省略括号**

  ```java
  enum Season{
      // 写第一行，Example调用无参构造器，等同于Example()
   	SPRING("春天"),SUMMER("夏天"),Example; 
      private String attr;
      private Season(String season){ //构造器私有，不允许其他外部类创建
          attr=season;
      }
      private Season(){}
  }
  ```

+ 使用`enum`关键字开发一个枚举类时，**默认会继承Enum类，而且是一个final类**

+ 由于`enum`类的父类是`Enum`，因此**直接输出`enum`类时**会调用`Enum`的`toString()`方法，而`Enum`中的`toString()`方法默认返回当前常量名，因此**会输出当前常量名**

  ```java
  public class Small {
      public static void main(String[] args) {
          // 输出BOY，因为它是常量名
          System.out.println(Season.Boy); 
      }
  }
  enum Season {
      Boy;
  }
  ```

+ **`enum`类不能再继承其他类，因为本质上它已经继承了`Enum`类。但是接口可以实现**

### Enum的方法 ###

```java
public class SmallChangeSys {
    public static void main(String[] args) {
        // 输出当前常量枚举类名，与直接输出Season.SPRING效果相同
        System.out.println(Season.SPRING.name());
        // 输出枚举对象的编号，按照定义从左至右顺序，从0开始
        System.out.println(Season.SPRING.ordinal());
        // 返回枚举类的所有枚举对象并放入数组中
        Season[] values = Season.values();
        // 将字符串转换成枚举对象，要求字符串必须为已有的常量名，否则报异常
        // 如下返回的是SPRING这个常量
        Season value = Season.valueOf("SPRING");
        // 前面的编号减去后面编号，如果小于0，说明前面的小；大于0，前面的大
        Season.SPRING.compareTo(Season.SUMMER);
    }
}
enum Season {
    SPRING("春天"),SUMMER("夏天"),AUTUMN("秋天"),WINTER("冬天");
    private String attr;
    private Season(String season){ //构造器私有，不允许其他外部类创建
        attr=season;
    };
    public String getAttr(){
        return attr;
    }
}
```

# 26. 注解 #

> 注解(Annotation)也被称为元数据，用于修饰解释包、类、方法、属性、构造器、局部变量等数据信息
>
> 和注释一样，注解不影响程序逻辑，但**注解可以被编译或运行，相当于嵌入在代码中的补充信息**
>

**元注解就是修饰注解的注解，主要有以下几个【主要用于阅读源码，了解即可】**

```java
// 指定该注解在哪些地方使用，取值含义可通过源码得到
@Target  
@interface // 表示当前类是一个注解
@Retention //指定该注解可以保留多长时间，其包含一个RetentionPolicy类型的成员变量，使用@Retention时必须为该value成员变量指定值
    RetentionPolicy.SOURCE //编译器使用后，自己丢弃这种策略的注释
    RetentionPolicy.CLASS //编译器将把注解记录在class文件中，当运行Java程序时，JVM不会保留注释。这是默认值
    RetentionPolicy.RUNTIME //编译器将把注解记录在class文件中，当运行Java程序时，JVM会保留注释，程序可以通过反射获取该注解
@Documented //修饰的注解类将被javadoc工具提取成文档，即生成文档时可以看到该注解
@Inherited //被修饰的注解具有继承性，即子类会有父类的注解
```

## @Override ##

+ **限定某个方法是重写的父类方法，该注解只能用于方法**

  ```java
  class A{
      public void test(){}
  }
  class B extends A{
      @Override   //表示这是一个重写的方法
      public void test() { }
      @Override   //此处会报错，因为注释校验后发现another()实际上不是重写的方法
      public void another() { }
  }
  ```

## @Deprecated ##

+ **用于表示某个程序元素(类、方法等)已过时【但是仍然可以使用】，使用此注释的属性或者方法被调用时会有一道横线**

+ 可以修饰方法、类、字段、包、参数等等，起到过渡作用【告诉别人这个方法已经过时了，去找找新方法】

  ```java
  public class SmallChangeSys {
      public static void main(String[] args) {
          A example = new A();
          int n=example.age;
          example.test();
      }
  }
  class A{  
      @Deprecated
      int age=0;
      @Deprecated
      public void test(){}
  }
  ```

  ![5](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171913099.png)

## @SuppressWarning ##

+ 对于一些语句，编译器会发出警告【不影响运行】

  ![6](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171913572.png)

+ 若不想看到警告，则可以使用注释`@SuppressWarning`**抑制编译器警告**

  ```java
  public class SmallChangeSys {
      @SuppressWarnings({"all"}) //all的意思是一致所有警告，若有多个参数则在大括号内用逗号隔开
      public static void main(String[] args) {
          List list = new ArrayList();
          list.add(1);
          System.out.println(list.get(0));
      }
  }
  ```

  [@SuppressWarnings字符串关键字明细](https://blog.csdn.net/huofuman960209/article/details/87689685?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161857616816780262529155%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=161857616816780262529155&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v29-3-87689685.first_rank_v2_pc_rank_v29&utm_term=%40supresswarning%E5%8F%82%E6%95%B0)
  
+ `@SuppressWarning`的抑制范围视其放置范围而定，**通常放置在具体的方法和类上**

# 27. 异常 #

> Java中，将程序执行中发生的不正常情况称为"异常"。【语法错误和逻辑错误不是异常】

**异常事件可分为两大类**

+ **Error(错误)：**Java虚拟机无法解决的严重问题，如JVM系统内部错误、资源耗尽等严重情况【如栈溢出和out of memory】，**Error是严重错误，程序会崩溃**

+ **Exception**：其他因编程错误或偶然外在因素导致的一般性问题，可以使用针对性代码进行处理。如空指针访问、试图读取不存在的文件、网络连接中断等等。

  `Exception`分为两大类：**运行时异常**和**编译时异常【==Exception下不在运行时异常就是编译时异常==】**

![7](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171913071.png)

+ **编译时异常必须要马上处理**，不然程序编译都无法通过【代码红色警告】，更不用说运行了

```java
// 空指针异常
NullPointException 
// 计算时异常，如除0
ArithmeticException 
// 数据越界
ArrayIndexOutOfBoundsException 
// 将一个类强转成另一个毫不相干的类抛出异常
ClassCastException 
// 当应用程序试图将字符串转换成一种数值类型，但该字符串不能转换为适当格式时，抛出该异常。使用异常可以确保输入时满足条件的数据
NumberFormatException 
```

## 异常处理 ##

### try-catch-finally ###

> 程序员自定义处理异常

```java
try{
    // 可能有异常的代码
}catch(Exception e){
    // 没有发生异常时，catch里的语句不会执行
    // 捕获到异常时，将异常封装成Exception对象e传递过来
    // 得到异常对象后自行处理
}finally{
    // 不管try代码块是否有异常发生，始终要执行finally
}
```

### throws ###

> 将发生的异常抛出，交给调用者(方法)来处理，最顶级的处理者就是`JVM`
>
> **若不采用try-catch，默认采用throws【只能处理运行异常，处理编译时异常要显示添加throws】**

![8](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171913688.png)

```java
public static void main(String[] args) throws NullPointException,ArithmeticException{
    //可以抛出多个异常，用逗号隔开
    }
```

+ **子类重写的方法，所抛出的异常类型要么和父类抛出的异常一致，要么为父类抛出异常类型的子类型**
+ 在`throws`过程中，如果有方法`try-catch`，就相当于处理异常，可不必再往上`throws`

## 自定义异常 ##

+ 自定义异常类名继承`Exception`或`RuntimeException`
  + 如果**继承Exception，属于编译异常**
  + 如果**继承RuntimeException，属于运行异常**(一般来说，继承`RunTimeException)`

```java
public class SmallChangeSys {
    public static void main(String[] args) {
        int age=10;
        if(age!=20){
            //抛出自定义异常，由于这是主方法，因此往上是JVM，JVM会打印异常信息
            throw new AgeException("年龄不是20！");
        }
        System.out.println("年龄是20！");
    }
}
class AgeException extends RuntimeException{
    public AgeException(String message) {
        //发生异常时，将传入的信息进行存储
        super(message);
    }
}
```

![9](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171913645.png)

## throws和throw ##

|            |           意义           |    位置    | 后面跟的东西 |
| :--------: | :----------------------: | :--------: | :----------: |
| **throws** |    异常处理的一种方式    | 方法声明处 |   异常类型   |
| **throw**  | 手动生成异常对象的关键字 |  方法体中  |   异常对象   |

# 28. 包装类 #

> 针对八种基本数据类型相应的引用类型（类对象），有了类的特点，就可以调用类中的方法

+ 除了`Boolean`与`Character`，其他包装类父类都是`Number`

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| boolean      | Boolean   |
| char         | Character |
| byte         | Byte      |
| short        | Short     |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |

![10](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171913367.png)

![11](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171913159.png)

## 装箱和拆箱 ##

```java
public class SmallChangeSys {
    public static void main(String[] args) {
        int n=6;
        // 手动装箱，即将数据手动放入构造函数中创建对象
        Integer integer = new Integer(n);
        // 手动拆箱，即通过调用方法将存入对象的数字取出
        int i = integer.intValue();
        // 自动装箱，底层自动调用Integer.valueOf(n)返回一个Integer对象
        Integer example=n;
        // 自动拆箱，底层帮助自动调用了intValue()
        int m=example;
    }
}
```

## Integer ##

```java
public class SmallChangeSys {
    public static void main(String[] args) {
        Integer i = new Integer(1);
        Integer j = new Integer(1);
        // 直接new为两个不同对象，false
        System.out.println(i==j); 
        // 自动装箱，实际上调用Integer.valueOf()方法
        Integer m=1;
        // valueOf()方法显示：如果是-128~127之间会直接返回
        Integer n=1;
        // 由于是直接返回现有数据且数值相同，返回true
        System.out.println(m==n);
        Integer x=128;
        Integer y=128;
        // 大于127，创建新对象
        System.out.println(x==y);
    }
```

# 29. 字符串相关类 #

## String ##

> String对象用于保存字符串，也就是一组字符序列。**字符串字符使用Unicode字符编码，一个字符占两字节**

```java
// String对象实际是用这个数组存放字符串内容
private final char value[]; 
// 因为这是一个final类型，所以一旦赋值就不能改变，即字符串赋值后不可修改【地址】
```

+ **直接赋值创建String对象的原理**：扫描常量池看看是否有当前字符串对应空间，若**有则使字符串指向已有空间**；若**没有则创建一个当前字符串对应空间并让String对象指向它**
+ **调用构造器创建String对象原理**：在堆中创建空间，里面维护了value属性，value**指向常量池的字符串空间**。如果常量池没有当前字符串，重新创建，如果有则直接通过value指向。但是**对象的地址指向的是堆中的空间地址**

```java
// 以下语句创建了几个对象？
String s1="hello";
s1="haha";
```

答：创建了两个对象。起初常量池没有对象，因此创建一个"hello"，又由于常量地址指向的内容是不可变的。所以当需要s1指向"haha"这个常量时，原先的内容并不会被抹除，而是会在常量池中寻找，发现没有找到，所以就在常量池创建"haha"并令s1指向。

```java
// 创建了几个对象？
String a="hello"+"abc";
```

只创建1个对象，因为编译器会对其进行优化，知道我们是想组合，因此不会为"hello"与"abc"单独划分空间，而是直接划一个"helloabc"的空间

```java
// 创建了几个对象？
String a="hello";
String b="abc";
String c=a+b;
```

a+b在底层实际是用Stringbuilder类来对a与b的内容进行append，最后调用toString()方法将其转换成String形式。所以c指向了堆的value，value指向了常量池中的"helloabc"。加之a与b，总共创建了3个对象。

## 常用方法 ##

```java
equals  //判断内容是否相等，区分大小写
equalsIgnoreCase //忽略大小写判断内容是否相等
indexOf //获取字符在字符串中第一次出现的索引，所以从0开始，找不到返回-1
lastIndexOf //获取字符在字符串中最后一次出现的索引，所以从0开始，找不到返回-1
trim //去除前后空格
chatAt //获取某索引处的字符
toUpperCase //转换成大写
toLowerCase //转换成小写
concat //将指定字符串连接到末尾
compareTo //按照字典顺序排序，负数说明当前字符串在字典前面
toCharArray //将字符串变字符数组
substring //截取字符串，包前不包后，填入参数为索引
```

```java
//format实现字符串输出格式化
public static void main(String[] args) {
        double money=12.356;
        String name="卓跃龙";
        String template=String.format("姓名%s，工资%.2f",name,money);
        System.out.println(template);
    //输出结果为：姓名卓跃龙，工资12.36
    }
```

## StringBuffer ##

> **==String保存的是字符串常量==**。里面的值不能更改，每次String类的更新实际上就是更改地址，效率低**【根本原因是存放存放字符串的`char[] value`是`final`修饰的】**
>
> **StringBuffer保存的是字符串变量**，里面的值可以更改，每次`StringBuffer`的更新实际上可以更新内容，不用每次更新地址，效率高【根本原因是存放存放字符串的`char[] value`**没有用**`final`修饰的】

![12](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171914012.png)

```java
//String和StringBuffer构造函数可以相互填入作为参数
append //追加内容，返回的还是StringBuffer。若str=null,append后的结果为null字符串
delete //删除指定范围的内容，同样是索引，包前不包后
replace //用指定内容替换索引范围内的内容，索引范围包前不包后
indexOf //查找子串第一次出现的索引
insert //指定位置插入字符串，原来的内容若有需要自动往后移
```

## StringBuilder ##

> 此类提供一个与StringBuffer兼容的API，但不保证同步（即**不是线程安全的**）。该类被设计用作StringBuffer的一个简易替换，**用在字符串缓冲区被单个线程使用的时候。如果可能，优先采用该类**

+ 如果字符串存在**大量的修改**操作，并在**单线程**情况下，使用**StringBuilder**
+ 如果字符串存在**大量的修改**操作，并在**多线程**情况下，使用**StringBuffer**
+ 如果字符串**很少修改且被多个对象应用，使用String**

# 30. Math #

```java
abs //绝对值
pow //求幂
ceil //向上取整
floor //向下取整
round //四舍五入
sqrt //求开方
random //求随机数，大于等于0.0，小于1.0
```

# 33. Arrays #

> 数组的封装类，没有final修饰，**可以继承重写方法**

```java
toString  //就数组元素变为字符串
sort //排序方式，可以传入匿名类Comparator自定义排序方式
binarySearch //二分搜索法进行查找(需要排好序)，返回目标值索引，找不到返回-1
copyOf //数组元素的复制，返回复制好的数组；copy(数组，长度)是放回根据长度返回复制的数字，若长度大于原数组则填入null
fill //数组元素的填充
equals //比较两个数组元素的内容是否完全一致
asList //将一组值转换成list
```

# 34. System #

```java
exit(0) //退出程序
currentTimeMillis //获得当前时间距离1970-1-1的毫秒
gc //运行垃圾回收机制
```

# 35. 存放大数字 #

> 保存比较大的数

```java
// 整数
BigInteger b = new BigInteger("1111111111111111111111111");
BigInteger b2 = new BigInteger("222222222221111111");
// 加减后是一个BigInteger对象
BigInteger add = b.add(b2); 
// 加法
System.out.println(add); 
// b2-b 
System.out.println(b2.subtract(b)); 
// b2*b 
System.out.println(b2.multiply(b)); 
// b2/b 
System.out.println(b2.divide(b)); 

// 浮点数乘除的精确值则需要借助BigDecimal，如下
// 结果最多保留5位，四舍五入模式
MathContext math =new MathContext(5, RoundingMode.HALF_UP);
BigDecimal decimal = new BigDecimal("2.4"); //值为2.4
decimal=decimal.divide(new BigDecimal("6"),math); //2.4除6，结果格式采用上边math的规定

BigDecimal a = new BigDecimal("12.123");
BigDecimal c = new BigDecimal("12.23");
System.out.println(a.divide(c,BigDecimal.ROUND_CEILING)); //结果保留分子的精度
```

# 36. 日期类 #

## Date ##

```java
Date date = new Date(); //获取当前日期
//年有四位，所以要4个y，月要输出两位，所以有2个MM
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy年MM月dd日 hh:mm:ss E");
String format = simpleDateFormat.format(date); //将格式化应用在日期上并返回格式后的字符串
System.out.println(format);

String s="2000年6月18日 10:20:30 星期三";
Date parse = simpleDateFormat.parse(s); //按照规定格式将字符串中的时间变回Date
System.out.println(parse);
```

![13](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171914714.png)

## Calendar ##

> 构造器私有，因此不能new。获得对象使用getInstance方法

```java
Calendar instance = Calendar.getInstance(); //获得当前日期对象
System.out.println(instance.get(Calendar.YEAR)+"年");
//月份+1才是真实的数据，因为这里是从0开始的
System.out.println(instance.get(Calendar.MONTH)+1+"月");
System.out.println(instance.get(Calendar.DAY_OF_MONTH)+"日");
System.out.println(instance.get(Calendar.HOUR)+"时"); //12进制，HOUR_OF_DAY是24进制
System.out.println(instance.get(Calendar.MINUTE)+"分");
System.out.println(instance.get(Calendar.SECOND)+"秒");
//没有提供格式化工具，需要自己根据需要进行组合
```

## LocalXXX ##

> LocalDateTime  封装当前日期时间；LocalDate 封装当前日期；LocalTime 封装当前时间

```java
//3个类都可以用now()获得当前时间
LocalDateTime now = LocalDateTime.now(); //获取当前时间和日期
System.out.println("年"+now.getYear());
//返回当前月份的英语
System.out.println("月"+now.getMonth());
//当前月份的数字
System.out.println("月"+now.getMonthValue());
//根据需要调用方法即可

//格式化形式
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 hh:mm:ss");
String format = dateTimeFormatter.format(now); //将时间放入格式化，返回格式化后的对象
System.out.println(format);

//200天后的日期
LocalDateTime localDateTime = now.plusDays(200);
//200天前的日期
System.out.println(now.minusDays(200));
//还有很多方法根据需要调用
```

## Instant ##

> 时间戳。同样是私有的，需要调用方法来获得对象

```java
Instant now = Instant.now();
        //可以将Instant转换成Date对象
        Date date = Date.from(now);
        //Date也可以转换成Instant
        Instant instant = date.toInstant();
```

# 37. 集合 #

> 动态存放任意多个对象，比较方便

## 单列集合 ##

> 单个元素构成的集合

![14](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171914146.png)

### Collections工具类 ###

> 多了个s，是工具类；没有s的是接口

```java
//仅List可用
reverse //反转lsit中的元素
shuffle //对链表元素随机排序
sort //按照自然顺序对链表进行排序，也可以实现Compartor接口自定义排序
swap(list,i,j) //将list中的i处元素和j处元素进行交换
copy(list1,list2) //list2拷贝到list1，要确保list1有足够空间
replaceAll(list,A,B) //将list中的A替换成B
    
//实现了Collection接口的都可用
max/min //根据自然排序返回最大元素，也可以实现Compartor接口自定义排序
frequency //指定元素出现次数
```

### Collection接口实现类 ###

> Collection本身是一个接口，不能被实例化，但是其中有许多方法。

```java
//参数均为对象，传入时会自动装箱
add //添加单个元素
remove //删除指定元素(可以填索引也可以填具体元素)
contains //查找元素是否存在
size //获取元素个数
isEmpty //判断是否为空
clear //清空
addAll //添加多个元素，参数为实现Collection的对象
containsAll //查找多个元素是否都存在，参数为实现Collection的对象
removeAll //删除多个元素，参数为实现Collection的对象
```

#### 迭代器Iterator ####

> 用于遍历Collection中的元素

**执行原理**

```java
Iterator iterator=coll.iterator();//得到迭代器
//hasNext() //判断是否还有下一个元素
while(iterator.hasNext()){//若没有下一个元素返回false
    //next() 1.指针下移 2.将下移后集合位置上元素返回
    //返回的元素是Object
    System.out.println(iterator.next());
}
//调用next()方法前必须调用hasNext()，否则当下一条记录无效时会抛出异常
//当退出while循环后，迭代光标会停在最后，如果要重新迭代需要再获得一次迭代器重置
```

+ 使用**增强for循环**也可以进行遍历

### List接口实现类 ###

+ 元素有序(即**添加顺序和取出顺序一致**)、且**可重复**
+ 集合中的每个元素都有 其对应的顺序索引，即支持索引访问

```java
add //默认添加到最后，可以指定位置添加，原本的元素会被往后挤
addAll //从开始索引位置起将集合内的所有元素加进来
get //获取指定位置的元素
indexOf //找到某个元素在链表首次出现中的位置
lastIndexOf //找到某个元素在链表最后一次出现的位置
remove //移除指定元素，可填索引也可具体元素
set //设置指定位置的元素
subList //返回开始索引到结束索引之间集合，包前不包后
```

#### ArrayList ####

+ **ArrayList可以加入null**
+ ArrayList是**由数组来实现数据存储**的
+ ArrayList基本等同于Vector，但是其线程不安全，不过**效率高**

##### 源码分析 #####

+ ArrayList中**维护了一个Object类型的数组elementData**

  ```java
  transient Object[] elementData; //transient表示该属性不会被序列化
  ```

+ 当创建ArrayList对象时，如果使用的是**无参构造器**，则**初始elementData容量为0，第一次添加，扩容elementData为10，如需再次扩容，则扩容elementData为1.5倍**

+ 如果使用的是**指定大小的构造器**，则初始elementData容量为指定大小，如果**需要扩容，则直接扩容elementData为1.5倍**

+ **每次add的时候都会检查空间是否充裕**，充裕就直接添加数据

#### Vector ####

+ Vector是线程同步的，即线程安全，其操作方法带有`synchronized`
+ 开发中，需要线程同步安全时，考虑使用Vector

##### 源码分析 #####

+ Vector中**维护了一个Object类型的数组elementData**
+ **无参构造器数组大小默认为10，满后按2倍扩容**
+ 如果**指定大小，则每次直接按2倍扩容**

#### LinkedList ####

> 底层实现了双向链表和双端队列

```java
getFirst/Last //获得首部或者尾部元素
get //获得指定索引元素
offerFirst/Last //添加元素
offer //默认添加到尾部
peekFirst/Last //检索头部或者尾部元素
peek //检索头部
pollFirst/Last //检索并删除头部或者尾部元素
poll //检索并删除头部元素
remove //删除指定位置元素，默认删除头部
```

##### 源码分析 #####

+ LinkedList中维护了两个属性`first`和`last`，分别指向**首节点和尾结点**
+ 每个节点维护了`prev(指向前一个)`、`next(指向后一个)`、`item(属性)`三个属性
+ **所以LinkedList元素的添加和删除不是通过数组完成，相对效率较高**

#### 如何选择？ ####

> 增删较多使用LinkedList；改查较多使用ArrayList
>
> 在一个项目中，根据业务灵活选择，可能一个板块用ArrayList，一个板块用LinkedList

### Set接口实现类 ###

#### HashSet ####

> 无序且无索引，不允许重复元素，最多包含一个null

+ **不能保证元素存入顺序和取出顺序是一致的**
+ **存储数据的是数组链表，当链表和数组达到一定长度时会将整个数组链表转换成红黑树**

##### 原理分析 #####

1. HashSet底层是HashMap

2. 添加一个元素时，**先得到hash值，通过算法转换成数组`table`索引值**

3. **找到存储数据表`table`，看这个索引位置是否已经存放的有元素。如果没有，直接加入；如果有，先判断当前元素hashCode()是否和数组第一个元素相同，不同则不操作；相同则调用存储类型的==`equals`方法【根据程序员重写进行比较】==比较，如果相同就放弃添加，若不相同则添加到最后**

   ```java
   //正常情况下，如果我们希望集合按照我们的意思存放相同元素，需要重新hashCode()和equals方法
   //可用快捷键Ctrl+Enter生成
   class Person{
       int age;
       String name;
   
       public Person(int age, String name) {
           this.age = age;
           this.name = name;
       }
   
       //当Person的名字和年龄一样就认为是equals的
       @Override
       public boolean equals(Object o) {
           if (this == o) return true;
           if (o == null || getClass() != o.getClass()) return false;
           Person person = (Person) o;
           return age == person.age && Objects.equals(name, person.name);
       }
   	//名字和年龄一样的Person，hashCode相同
       @Override
       public int hashCode() {
           return Objects.hash(age, name);
       }
   }
   ```

4. 如果**一条链表的元素个数达到8且数组长度达到64，就会将整个结构进行红黑树化**

5. **每添加一个元素【不管加到链表还是数组】进入数组链表就会检查是否需要扩容【初始大小16】，当元素个数达到数组长度的0.75倍时就会进行扩容(扩大2倍)**

#### LinkedHashSet ####

1. LinkedHashSet是HashSet的子类，其**底层是一个LinkedHashMap，维护了一个数组+双向链表**
2. LinkHashSet**根据元素的hashCode来决定元素的存储位置**，同时使用链表维护元素的次序，使得元素看起来是以插入顺序保存的

##### 原理分析 #####

+ LinkedHashSet中维护了一个hash表和双向链表**(LinkedHashSet有head和tail)**

+ 每一个节点有**before和after属性，这样可以形成双向链表**

+ 添加一个元素时，**先求hash值，再求索引，确定该元素在hashtable的位置，然后将添加的元素加入到双向链表(如果已经存在则不添加)**

+ `next`指针依然存在，不过其指向的是当前数组链表中的后一个元素

  ```java
  tail.next=newElement;
  newElement.pre=tail;
  tail=newElement;
  ```

+ 这样我们遍历`LinkedHashSet`也能**确保插入顺序和遍历顺序一致**

#### TreeSet ####

> 自定义排序的集合，本质上是`TreeMap`

##### 原理分析 #####

+ 在添加元素的时候，会调用我们传入的`Comparator`匿名内部类中的`compare`方法，根据返回值决定是将当前元素放在`left`【返回值小于0】还是`right`【返回值大于0】。**==当返回值等于0时，Key值不发生操作，但是Value值会被替换【对于集合来说不进行任何操作，因为集合存放的数据都在Key】==**
+ **形成的树实际上是二叉树搜索树【树节点是Map.Entry】，遍历时候根据树的结构中序输出就是结果**
+ **没有传入匿名类实现Comparator时，TreeSet会调用当前类实现Comparator接口的compareTo方法来进行排序；若存入的类没有实现Comparator接口，则会报错。因为底层会有向上转型到Comparator**

## 双列集合 ##

> 存放的是Key-Value键值对

![15](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171914820.png)

### Map接口实现类 ###

+ Map与Collection并列存在，用于保存具有映射关系的数据：Key-Value
+ Map中的**key不允许重复，value允许重复**
+ Map的**key可以为null，value也可以为null**；但是key为null只能有一个，value为null可以有多个
+ **同样的key添加进去的数据会覆盖原有的数据【Key不替换，Value会替换】**

#### 原理分析 ####

+ 本质上是`table`维护的一个**数组链表，每一个元素是一个Node**

  ```java
  transient Node<K,V>[] table 
      //节选
      static class Node<K,V> implements Map.Entry<K,V> { //实现了Map.Entry<K,V>接口
          final int hash;
          final K key;
          V value;
          Node<K,V> next;
      }
  ```

+ 为了方便程序员遍历，**底层会创建EntrySet<Entry<K,V>>集合，该集合存放的元素类型是Entry<K,V>**。可以通过`map.entrySet()`获得这个集合，里面有包含有Node的key和value

  ```java
  map.entrySet() //存放key和value的集合	
  map.keySet(); //存放key的集合
  map.values(); //存放values的集合(Collection)
  ```

+ **Node实现了接口Entry<K,V>，通过向上转型，EntrySet存放的Entry<K,V>实际上是Node，相当于Node把key和value的地址放在集合中。当遍历集合元素时通过地址指向table**

+ **Entry<K,V>接口**提供了方法`getKey()与getValue()`，其**指向**`table`中的`key`和`value`值

![16](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171914011.png)

#### 常用方法 ####

```java
put //添加映射关系
remove //根据key删除映射
get //根据key获取value
size //获得映射个数
isEmpty //判断map是否为空
clear //清楚map内容
containsKey/Value //是否存在key/Value值
```

#### 遍历方式 ####

1. **得到keySet，利用增强for循环遍历keySet，通过键值得到value**

2. **通过得到keySet的itertor获得keySet，通过键值得到value**

3. **得到Values，遍历得到所有的Value(同样可以使用迭代器)**

4. **得到entrySet，利用Map.Entry将其中的元素进行接收，并通过`getKey()和getValue()`方法来获得值**

   ```java
   		Map<String,String> map = new HashMap<String,String>();
   		//利用Map.Entry将其中的元素(Node)进行接收
           for (Map.Entry<String, String> entry : map.entrySet()) {
               String key = entry.getKey();
               String value = entry.getValue();
           }
   ```

### HashMap ###

> 扩容树化原理与HashSet一致，因为HashSet底层就是HashMap

### HashTable ###

> 使用方法与HashMap基本一致，不过HashTable是线程安全的且扩容机制是按照(2倍+1)进行扩容。效率较HashMap较低
>
> **HashTable的key和value都不能为null**

### Properties ###

> 专门用于读写配置文件的集合类
>
> **配置文件格式：键=值**【键值对不需要有空格，值不需要用引号引起来，默认类型是String】

+ **Properties类继承自HashTable并且实现了Map接口**，也是一种键值对的形式保存数据，使用特点与HashTable类似
+ **Properties还可以用于从xxx.properties文件中，加载数据到Properties对象，并进行读取和修改**

#### 常用方法 ####

```java
load //加载配置文件的键值对到Properties对象
list //将数据显示到指定设备
getProperty(key) //根据键获取值
setProperty(key,value) //设置键值对到Properties对象，没有则创建

//创建新的Properties
store //将Properties中的键值对存储到配置文件，如果有中文会存储为Unicode码
```

#### 实际例子 ####

```java
public static void main(String[] args) throws Exception {
        Properties properties = new Properties();
        properties.setProperty("user","tom"); //设置一个键值对
        //将properties的东西输出到指定位置；null部分为注释信息，一般不写
        properties.store(new FileOutputStream("src\\mysql2.properties"),null);
        System.out.println("成功");

        Properties properties2 = new Properties();
        //将目标地址的文件加载下来
        properties2.load(new FileInputStream("src\\mysql2.properties"));
        String user = properties2.getProperty("user");//根据Key找Value
        System.out.println("我是被加载的："+user);
        
        properties2.setProperty("pwd","123"); //新建立一个键值对
        //将properties2的数据写到指定地址
        properties2.store(new FileOutputStream("src\\mysql2.properties"),null);
    }
```

### TreeMap ###

> **自定义排序(Key)的K-V映射**。原理部分与TreeSet一致【TreeSet本质是TreeMap 】
>
> 可以在类声明时通过一个标识表示类中某个属性的类型，或者是某个方法的返回值类型，或者是参数类型

# 38. 泛型 #

> 能够表示数据类型的数据类型【如泛型E可以代表Integer，也可以代表String】
>
> 类和接口都可以加泛型，多个泛型之间用逗号隔开

```java
//创建对象的时候动态传入类型，所有T的部分都会变成动态传入的类型
class Person<T>{
    T s;

    public Person(T s) {
        this.s = s;
    }
    public T f(){
        return s;
    }
}
//T具体的数据类型在定义Person对象的时候指定，即在编译期间，就确定E是什么类型
Person<String> person = new Person<String>("j"); //这样是正确的
Person<Integer> person = new Person<Integer>("j");//构造函数要求传入的是T类型，编译类型确定了T类型是Integer，因此参数不是Integer就会报错
```

+ 泛型要求指向的数据类型是引用类型，**不能是基本数据类型**

+ 给泛型指定具体类型后，**参数是泛型的地方可以传入该类型或者其子类型**

  ```java
  //假设A是B的父类
  Pig<A> pig=new Pig<A>(new A()); //这是对的，T为A类
  Pig<A> pig=new Pig<A>(new B()); //这也是对的，T为A的子类B
  class Pig<T> {
  public Pig(T t) {}
  }
  ```

+ 编译器会进行自动类型推断

  ```java
  Person<String> person = new Person<>();//当省略执行类型的泛型时，编译器默认其泛型为编译类型的泛型，即String
  ```

## 自定义泛型 ##

```java
class 类名<T,R,...>{ //表示可以多个泛型
    成员
}
```

+ 普通成员【属性，方法】可以使用泛型
+ **使用泛型的数组不能初始化【即初始化必须在方法里完成】**
+ **静态属性和方法中不能使用类的泛型**
+ 泛型类的类型是在创建对象的时候确定的
+ 如果在创建对象时没有指定类型，默认为Object

```java
interface 接口名<T,R,...>{
    //接口中属性默认都是final static ，因此都不能用泛型
}
```

+ 接口中，静态成员也不能使用泛型
+ **泛型接口的类型，在继承接口或者实现接口时确定**

## 自定义泛型方法 ##

```java
修饰符 <T,R,...>返回类型 方法名(参数){
    //返回类似可以使用泛型，参数中也可以使用泛型
}
```

+ 泛型方法**可以定义在普通类中，也可以定义在泛型类中**

+ **泛型方法可以使用类定义的泛型**

+ **当泛型方法被调用时，类型会确定**

  ```java
  public class Generic {
      public static void main(String[] args) {
          Person person = new Person();
          person.f("你好"); //传入字符串，T就被认为是String
          person.f(1); //传入整型，T就被认为是Integer
      }
  }
  class Person {
      public <T> T f(T n) {
          return n;
      }
  }
  ```

+ `public void eat(E e)`修饰符后没有`<T,R,...>`，**因此该方法不是泛型方法，而是使用了泛型**

## 泛型的继承和通配符 ##

+ **泛型不具备继承性**

  ```java
  List<Object> list=new ArrayList<String>(); //这样是错误的
  ```

+ ```java
  //泛型的其他形式
  <?> ：支持任意泛型类型
  <? extends A>:支持A类及A类的子类，规定了泛型上限
  <? super A>：支持A类以及A类父类，不限于直接父类，规定了泛型下限
  ```

# 39. Java绘图坐标体系 #

> Java的图形化设计以JFrame类为框架【可以**理解为画板**】，Jpanel类为面板【**理解为画纸**】。
>
> **X-Y坐标轴以左上角为原点，右边是X轴正向；Y轴下方是正向**

+ JPanel上的画图由`paint(Graphics g)`方法实现，其中**==g可以理解为画笔==**。Graphics是一个接口，在JPanel中实现了初始化，我们重写的时候收到的实际是一个上转型对象，所以可以直接调用方法使用。

+ **以下情况会自动调用paint()方法**

  1. 窗口最小化再最大化
  2. 窗口大小发生变化
  3. `repaint`函数【刷新组件外观方法】被调用

+ 使用画笔进行画图时经常需要标明坐标位置以及图像的高宽，**高宽是在坐标位置基础上计算的，坐标位置如箭头所示**

  ![17](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171914227.png)

## Graphics类 ##

```java
setColor(颜色) //设置画笔颜色
drawLine(int x1, int y1, int x2, int y2) //(x1,y2)到(x2,y2)间连线【直线】
drawRect(int x, int y, int width, int height) //画矩形，(x,y)确定开始位置
drawOval(int x, int y, int width, int height) //画椭圆，高宽相等时是圆
fillOval(int x, int y, int width, int height) //根据画笔颜色填充椭圆
fillRect(int x, int y, int width, int height) //根据画笔颜色填充矩形
setFont(new Font("楷书",Font.BOLD,50)); //设置画笔字体、粗细、大小
drawString("皇后",int x,int y); //用当前画笔写字，(x,y)是字左下角位置
    
//添加图片，图片需要放在项目下
Image image = Toolkit.getDefaultToolkit().getImage(Panel.class.getResource("/根目录下路径"));
drawImage(image,int x,int y,int width,int height,this);//在当前对象处显示图片
```

# 40. 线程 #

> **进程是指运行中的程序**，比如我们使用QQ，就启动了一个进程，操作系统就会为该进程分配内存空间；启动迅雷时，又启动一个进程，操作系统为迅雷分配新的内存空间
>
> 进程是程序的一次执行过程，或是正在运行的一个程序，是动态过程。**有它自身的产生、存在和消亡过程**
>
> **线程是由进程创建的，是进程的一个实体<br>一个进程可以拥有多个线程**

## 线程实现 ##

> ①继承Thread类【本质上也是实现Runnable接口】	②实现`Runnable`接口

![18](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171915086.png)

```java
class A extends Thread{
    public void run(){}
}
new A().start(); //创建线程并调用run方法

class B implements Runnable{
    public void run(){}
}
new Thread(b).start(); //动态绑定run方法，创建线程并调用run
```

### 1. 线程中止 ###

+ **如何让主线程控制子线程中止？**

  > 可以通过修改子线程的变量。如子线程有一变量`private boolean loop`，当其为`true`时无限循环；**主线程启动子线程后，休眠一段时间，而后调用setXXX方法修改loop**，使其成为`false`，由此达到中止子线程的目的

### 2. 线程常用方法 ###

```java
setName //设置线程名称
getName //返回该线程名称
getState //获得当前状态
start //使线程开始执行，Java虚拟机底层调用该线程的start0方法
run //调用线程run方法
setPriority //更改线程优先级
getPriority //获取线程优先级
sleep //在指定毫秒数内让当前正在执行的线程休眠
interrupt //中断线程，没有真正结束线程，一般用于唤醒线程【即用于中断正在休眠线程】
```

+ **睡眠中断例子**

```java
public class Interrupt {
    public static void main(String[] args) {
        A a = new A();
        a.start();
        try {
            System.out.println("3秒钟后我就唤醒线程，不给睡50秒");
            Thread.sleep(3000);
            a.interrupt(); //线程中断命令
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
class A extends Thread{
    public void run(){
        while (true){
            try {
                System.out.println("我要睡50秒！");
                Thread.sleep(50000);
            } catch (InterruptedException e) {
                System.out.println("我被中断，这次睡眠结束，但我会回来的！");
            }
        }
    }
}
```

+ **用于插队的方法**

```java
yield //线程礼让，但是礼让的时间不确定，因此不一定成功【当CPU资源足够的时不需要礼让，因此就会失败】
join //线程插队。插队一旦成功，则肯定先执行完插入线程的所有任务
```

+ 守护线程是为工作线程服务的，**当所有的用户线程结束，守护线程自动结束【如垃圾回收机制，当所有线程都结束时才结束，期间一直发挥作用】**

  ```java
  setDaemon(true) //将一个线程设置为守护线程
  ```

## 线程实现原理 ##

+ 当我们启动程序的时候，进程就被创建了，同时会开启**`main`线程**，线程中可以创建其他线程【套娃】……**多个线程之间共享内存时，轮流享受CPU服务。当进程创建的所有线程都消亡后，进程也消亡。**

+ ==既然最后都是实现run()方法，为什么继承Thread的类要通过start()来调用而不直接调用run()呢？==

  > 当**直接调用run()方法**时，本质上就是调用了一个成员方法，与调用一个普通类的成员方法并无二异，**不能达到创建新线程的效果**，且由于调用了方法，因此会等到run()执行完才往下走。
  >
  > start()方法**底层通过start0()方法创建了一个新线程，再通过这个新线程调用run()**方法。即**对于原线程来说，当新线程创建成功之后，start语句就完成了，因此会继续往下执行。而新创建的线程则在资源分配过来后执行run()方法**
  
+ ==为什么Thread构造器传入Runable接口实现类后，调用Thread的start就可以动态绑定到此类的run方法？==

  ```java
  //Thread调用start()，实际上是调用start0()方法创建线程
  //start0()调用run方法，如果此时target有值，则在线程中可以调用
  private Runnable target;	//Thread的一个私有属性
      public void run() {
          //Thread中的run方法，当构造函数传入target时，target不为空
          if (target != null) {
              target.run();
          }
      }
  //我们继承Thread时往往重写run方法，因此上面的run会被我们自定义的run重载
  ```

![19](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171915072.png)

## 线程同步 ##

**为什么需要同步？**

> 在多线程编程，一些敏感数据不允许被多个线程同时访问，此时就使用同步访问技术，**保证数据在任何同一时刻，最多有一个线程访问，以保证数据的完整性**
>
> **当有一个线程在对内存进行操作时，其他线程都不可以对这个内存地址进行操作，直到该线程完成操作**，其他线程才能对该内存地址进行操作

### Synchronized ###

> 被此关键字修饰后，多个线程访问同一方法时必须等其中一个线程操作完，另一个线程才能进入操作

```java
//同步代码块
//对象上锁，直至代码块执行完毕才解锁
synchronized (对象){	//对象没上锁才能操作同步代码
    //需要被同步的代码    
}
//synchronized还可以放在方法声明中，表示整个方法为同步方法
访问修饰符 synchronized void 方法名(参数){
    //需要被同步的代码 
}
```

### 何时会释放锁 ###

+ 当前线程**同步方法、同步代码块执行结束**
+ 当前线程在同步代码块、同步方法中遇到**break、return**
+ 当前线程在同步代码块、同步方法中**出现了未处理的Erro或者Exception**，导致异常结束
+ 当前线程在同步代码块、同步方法中**执行了线程对象的wait方法**，当前线程暂停并释放锁

### 以下情况不释放锁 ###

+ 线程执行同步代码块或者同步方法时，程序**调用sleep、yield方法暂停当前线程执行，不释放锁**
+ 线程执行同步代码块，其他线程**调用了该线程的suspend方法【此方法不再推荐使用】将该线程挂起，该线程不会释放锁**

## 线程死锁 ##

> 多个线程都占用了对方的锁资源，但不肯相让，导致死锁

# 41. 文件 #

> 文件在程序中是以流的形式来操作的

![20](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171915019.png)

## File类 ##

> 用于Java与文件进行连通的类

```java
File file = new File("f:\\text1.txt"); //直接写出所有路径
File file1 = new File("f:\\"); //将父路径放在File中
File file2 = new File(file1, "text2.txt"); //根据File保存的父路径创建文件
File file3 = new File("f:\\", "text3.txt");//指定父路径与子路径
try {
    file.createNewFile(); //创建方法
    file2.createNewFile();
    file3.createNewFile();
    System.out.println("创建成功");
   } catch (IOException e) {
     e.printStackTrace();
}
```

### 常用方法 ###

```java
getName() //获取文件名
getAbosolutePath() //获取绝对路径
length //获取文件大小(字节)
exists //文件是否存在
isFile //是不是一个文件
isDirectory //是不是一个目录
    
mkdir //创建一级目录
mkdirs //创建多级目录
delete //删除空目录或文件
```

# 42. I/O流 #

+ 按操作数据单位不同分为：①==**字节流(传输二进制文件)**== ②==**字符流(传输文本文件)**==
+ 按数据流的流向不同分为：①输入流 ②输出流
+ 按流的角色不同分为：①节点流 ②处理流/包装流

| 抽象基类 |    字节流    | 字符流 |
| :------: | :----------: | :----: |
|  输入流  | InputStream  | Reader |
|  输出流  | OutputStream | Writer |

+ Java的I/O流共涉及40多个类，实际上非常规则，都是**从如上4个抽象基类派生的**
+ 由这四个类派生出来的子类名称都是以其父类名作为子类名后缀

## InputStream ##

### FileInputStream ###

> FileInputStream可以绑定一个File对象【或文件路径名称】，成为这个文件的输入流，通过read()方法将文件读取到内存中。
>
> **最后要调用close()方法关闭流**

```java
		File file = new File("g:\\text1.txt");
        FileInputStream fileInputStream=null;
        try {
            fileInputStream = new FileInputStream(file);//绑定输入流
            int read;
            while ((read=fileInputStream.read())!=-1){
                //说明有字符
                System.out.println((char) read);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
```

+ **read参数可以填入byte数组，意思为一次读取数组大小的字节流，这样效率更高**

  ```java
  int len=0;
  byte[] bytes=new byte[8]; //接收的数组
  while ((len=fileInputStream.read(bytes))!=-1){
  //len为目标读取到bytes的字节长度
  System.out.println(new String(bytes,0,len));
  }
  ```

### BufferedInputStream ###

> 属于处理流，用于封装实现了InputStream抽象类的对象，提高操作效率

## OutputStream ##

### FileOutputStream ###

> FileOutputStream可以绑定一个File对象【或文件路径名称】，成为这个文件的输出流，通过write()方法将内存中的数据写到文件中。
>
> **write()方法参数可以是字符，也可以是byte数组【String对象有getBytes()方法可以方便得到】，同样可以设置byte数组写入文件的长度**

```java
String path="g:\\text1.txt";
FileOutputStream out=null;
try {
out = new FileOutputStream(path,true);//为true时不会覆盖原先的内容，默认会覆盖
out.write(97); //将ASKII码97的字符写入
} catch (Exception e) {
e.printStackTrace();
}  finally {
try {
	out.close();
} catch (IOException e) {
    e.printStackTrace();
       }
 }
```

### BufferedOutputStream ###

> 属于处理流，用于封装实现了OutputStream抽象类的对象，提高操作效率

## Reader ##

### FileReader ###

> 按照字符来操作I/O，是InputStreamReader的子类

```java
//构造函数同样是绑定File或者路径
read(char[]) //批量读取多个字符到数组，返回读取到的字符数，如果到文件末尾返回-1。不填入参数的话则是读取单个字符，返回的为此字符ASKII码

```

### BufferedReader ###

```java
//包装其他的节点流或处理流，内有一些封装方法，能够更高效率的对数据进行获取 
BufferedReader bufferedReader = new BufferedReader(new FileReader("g:\\2.jpg"));
readLine() //读取一整行字符，返回的内容是读取到的字符串
```

## Writer ##

### FileWriter ###

> 按照字符来操作I/O，是OutputStreamWriter的子类

```java
write(char[],off,len) //写入指定数组的指定部分
//参数也可以写入单个字符或者字符串以及指定字符串部分
```

+ **==FileWriter使用后，必须要关闭(close)或者刷新(flush)，否则写入不到指定的文件==**

### BufferedWriter ###

```java
//包装其他的节点流或处理流，内有一些封装方法，能够更高效率的对数据进行获取 
BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter("g:\\1.jpg"));
write() //写入方法没有怎么变化
newLine() //插入一个换行
```

## 节点流和处理流 ##

> **节点流可以从一个特定的数据源读写数据**，即知道数据来源是什么【如FileReader数据来源就是文件】
>
> **处理流【包装流】是"连接"在已存在的流(节点流或处理流)之上，为程序提供更为强大的读写功能**【如BufferedReader可以封装实现了Reader的对象，因此使用更加灵活】

### 序列化和反序列化 ###

1. **序列化**就是在保存数据时，==**保存数据的值和数据类型**==
2. **反序列化**就是在恢复数据时，==**恢复数据的值和数据类型**==
3. 需要让某个对象支持序列化机制，则必须让其类是可序列化的，为了让某个类是可序列化的，该类必须实现**Serializable接口**

### ObjectXXputStream ###

> 同样是处理类，配合FileXXputStream后可以**==将可序列化的对象存放在文件中==**。当然在需要的时候也可以取出来，**按照什么顺序存入就要按照什么顺序取出**

```java
public class Test {
    public static void main(String[] args) {
        ObjectOutputStream out = null;
        ObjectInputStream in = null;
        try {
            out = new ObjectOutputStream(new FileOutputStream("g:\\res.tex"));
            in = new ObjectInputStream(new FileInputStream("g:\\res.tex"));
            out.writeObject(new Dog());//将这个Dog对象写入文件
            out.writeInt(10); //将整型Integer对象取值为10写入文件
            Object dog = in.readObject(); //读出文件的Dog对象
            int i = in.readInt(); //按顺序读出Integer对象
            Dog dog1=(Dog) dog;
            System.out.println(dog1.name);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (out != null) {
                    out.close();
                }
                if (in != null) {
                    in.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
//Dog对象实现了序列化
class Dog implements Serializable {
    int age=18;
    String name="Tom";
}
```

+ 读写顺序要一致
+ 要求实现序列化或反序列化的对象，需要实现Serializable
+ **序列化对象时，除了static或transient修饰的成员【即这两者不会被保存】，默认将里面所有属性都进行序列化**
+ **序列化对象时，要求里面属性的类型也需要实现序列化接口**
+ **序列化具有可继承性**，也就是如果某类已实现序列化，则它的所有子类也已经默认实现序列化

## 标准I/O流 ##

```java
//编译类型是InputStream，运行类型是BufferedInputStream
System.in //标准输入流(从键盘获取)
//编译类型是PrintStream，运行类型也是PrintStream
System.out //标准输出流(输出到键盘上)
```

## 转换流 ##

> InputStreamReader和OutputStreamWriter
>
> 转换流能**将字节流转换成字符流**，并且规定转换时的编码方式

+ 当处理纯文本数据时，如果使用字符流效率更高，并且**可以有效解决中文问题**。因此处理这类数据建议将字节流转换成字符流
+ 可以在使用时**指定编码格式**(如UTF-8，gbk等)

```java
//接收进来的原本是文件输入流，加以包装转换为字符流并规定编码方式为gbk
//再将字符流用缓冲流封装提高效率，最后只需要对缓冲流操作即可
BufferedReader reader = new BufferedReader(new InputStreamReader(new FileInputStream("g:\\text1.txt"), "gbk"));   
//输出流同理
```

## 打印流 ##

> **打印流只有输出【字节输出和字符输出】，没有输入**

#### PrintStream(字节) ####

```java
public static void main(String[] args) throws Exception {
 PrintStream out = System.out; //获得标准打印流
 out.println("123");//由于是标志打印流，所以会输出在控制台
 out.write("哈哈哈".getBytes()); //print方法本质是write，因此直接用write效果一直，参数是字节
 System.setOut(new PrintStream("g:\\text1.txt")); //设置标准打印流的打印位置为G盘的text1.txt
 System.out.println("good"); //通过设置后，此时会打印在G盘的text1.txt
    }
```

#### PrintWriter ####

```java
//System.out是字节流，因此需要通过构造函数将其变为字符流
PrintWriter writer = new PrintWriter(System.out);
PrintWriter writer2=new PrintWriter("G:\\text1.txt"); //指定输出位置
```

# 43. 网络编程 #

## 1. InetAddress ##

```java
//获取本机的InetAddress对象(主机名/IP地址)
InetAddress localHost1 = InetAddress.getLocalHost();
//根据主机名获取InetAddress对象(主机名/IP地址)
InetAddress name = InetAddress.getByName("捉妖龙");
//根据域名获取InetAddress对象(主机名/IP地址)
InetAddress name2 = InetAddress.getByName("www.baidu.com");
//根据InetAddress对象获取IP地址
String address = name.getHostAddress();
//根据InetAddress对象获取主机名或域名
String hostName = name.getHostName();
```

## 2. TCP(客户服务器模式) ##

> **服务器先对端口进行监听ServerSocket.accept()，当此端口有客户来连接的时就建立起一个Socket通道**。<br>双方**通过Socket获得输入流与输出流**，由此进行信息交互

**==注意：每次使用write()方法时要有添加结束符，否则客户机与服务器可能会死锁==**

对字符流添加结束符的方式有

### 字节流传送 ###

> 对**字节流添加结束符的方式**是**调用socket.shutdownOutput()方法**<br>若用**Buffered**对socket.getOutputStream()得到的输出流进行**封装**，则==使用shutdownOutput()方法前==需要用**flush()方法将缓冲流推上通道**

```java
public class ClientDemo {
    public static void main(String[] args) throws IOException {
        //客户端要往本机9999端口发送东西
        Socket socket = new Socket(InetAddress.getLocalHost(), 9999);
        OutputStream out = socket.getOutputStream();
        InputStream in = socket.getInputStream();
        byte []bytes= new byte[20];
        int len;
        out.write("Hello,World".getBytes());
        socket.shutdownOutput();//结束符

        while ((len=in.read(bytes))!=-1){
            System.out.println("客户端收到："+new String(bytes,0,len));
        }

        out.close();
        in.close();
        socket.close();
    }
}
```

```java
public class ServerDemo {
    public static void main(String[] args) throws IOException {
        //对9999端口进行侦听
        ServerSocket serverSocket = new ServerSocket(9999);
        System.out.println("客户机侦听中……");
        //等待连接，有连接后得到socket
        Socket socket = serverSocket.accept();
        byte []bytes= new byte[20];
        int len;
        InputStream in = socket.getInputStream();
        OutputStream out = socket.getOutputStream();

        while ((len=in.read(bytes))!=-1){
            System.out.println("服务端收到："+new String(bytes,0,len));
        }
        out.write("Hello".getBytes());
        socket.shutdownOutput(); //加上结束符

        in.close();
        out.close();
        socket.close();
        serverSocket.close();
    }
}
```

### 字符流传送 ###

> 由于socket只能得到**字节流**，因此若要得到**字符流需要转换流的介入**。
>
> 字符流除了传统的方法加结束符外还可以用如下方法添加↓
>
> 转换流封装后需要缓冲流Buffered再进行封装，此时**加入结束符的方式是BufferedWriter每次调用完write方法后就调用newLine()方法**，意思为加上换行符，即结束符。同时对面**接收时要使用readLine()方法**
>
> **==字符流需要手动调用flush()才能会将流推上Socket通道，而字节流再加上结束符后会自动推上通道==**

```java
public class ClientDemo {
    public static void main(String[] args) throws IOException {
        //客户端要往本机9999端口发送东西
        Socket socket = new Socket(InetAddress.getLocalHost(), 9999);
        BufferedWriter out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
        BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        out.write("good");
        out.newLine();//加结束符
        out.flush(); //将数据推上通道
        String s = in.readLine();
        System.out.println("客户端收到的数据：" + s);

        in.close();
        out.close();
        socket.close();
    }
}
```

```java
public class ServerDemo {
    public static void main(String[] args) throws IOException {
        //对9999端口进行侦听
        ServerSocket serverSocket = new ServerSocket(9999);
        System.out.println("服务器侦听中……");
        //等待连接，有连接后得到socket
        Socket socket = serverSocket.accept();
        BufferedWriter out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
        BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        String s = in.readLine();
        System.out.println("服务器收到的数据：" + s);
        out.write("excellent");
        out.newLine();//加结束符
        out.flush(); //将数据推上通道

        in.close();
        out.close();
        socket.close();
        serverSocket.close();
    }
}
```

### 传输文件应用 ###

#### 要求 ####

1. **客户**从指定地区**读取文件并**通过Socket发送给服务器	
2. **服务器收到客户的文件后将其写入src下，并告诉客户已经收到文件**	
3. **客户接收到服务器的消息并显示**，整个文件传输完成

#### 设计思路 ####

+ 首先我们**传入Socket通道的应当是完整的文件**，即发送前应该在客户端部分将读取来的零碎字节流组装成一个字节数组bytes[]。同理，服务器将接收的图片**写入src下时，也应该是完整的文件**。由此，设计一个**将输入流中零碎字节组装成一个完整字节组**的方法很有必要

  ```java
  //根据输入流获得文件的完整字节数组
  public static byte[] streamToByteArray(InputStream in) throws IOException {
          BufferedInputStream bis = new BufferedInputStream(in);
          ByteArrayOutputStream bos = new ByteArrayOutputStream();
          int len;
          byte[] bytes = new byte[1024];
          while ((len = bis.read(bytes)) != -1) {
              bos.write(bytes, 0, len);
          }
          byte[] res = bos.toByteArray();
          return res;
      }

+ 整个逻辑过程如下
  1. 客户端通过输入流将完整文件放到内存中，然后**通过输出流上传至Socket通道**
  2. 服务器通过输入流得到Socket通道的文件流，并且将其拼接成完整字节数组；接着通过输出流将这个字节数组存放到src下，同时通过另一个输出流将应答信息上传至Socket通道
  3. 客户端通过另一个输入流接收到Socket的内容并输出。
+ **==整个过程中设计到Buffered的write方法都要调用flush()函数；若是将数据写入Socket通道中，则在flush后还需要调用socket.shutdownOutput()方法为其加上结束符==**

#### 实例 ####

```java
public class Client {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket(InetAddress.getLocalHost(),8888);
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("g:\\picture_1.jpg"));
        BufferedOutputStream bos = new BufferedOutputStream(socket.getOutputStream());
        BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));

        //图片的字节数组
        byte[] bytes = Util.streamToByteArray(bis);
        bos.write(bytes);
        bos.flush();
        socket.shutdownOutput();
        String s = reader.readLine();
        System.out.println("来自服务器消息："+s);

        bis.close();
        bos.close();
        reader.close();
        socket.close();
    }
}
```

```java
public class Server {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8888);
        System.out.println("服务器侦听8888端口……");
        Socket socket = serverSocket.accept();
        BufferedInputStream bis = new BufferedInputStream(socket.getInputStream());
        byte[] bytes = Util.streamToByteArray(bis);
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("src\\copy.jpg"));
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
        bos.write(bytes); //写进目标位置
        bos.flush();
        writer.write("图片已正确接收");
        writer.newLine();
        writer.flush();

        bis.close();
        bos.close();
        writer.close();
        socket.close();
        serverSocket.close();
        System.out.println("服务器结束");
    }
}
```

## 3. UDP通信 ##

1. 类DatagramSocket和DatagramPacket【数据报】实现了基于UDP协议网络程序
2. UDP数据报通过**数据报套接字DatagramSocket**发送和接收，系统不保证UDP数据报一定能够安全送到目的地，也不能确定什么时候可以抵达
3. **DatagramPacket对象封装了UDP数据报【装包】，在数据报中包含了发送端的IP地址和端口号以及接收端的IP地址和端口号；相要看包里的内容就需要进行拆包**
4. UDP协议中每个数据报都给出了完整的地址信息，因此无须建立发送方和接收方连接

```java
public class Receive {
    public static void main(String[] args) throws IOException {
        //监听9998端口
        DatagramSocket socket = new DatagramSocket(9998);
        byte[] bytes=new byte[1024];
        //用指定字节数组作为包的容器
        DatagramPacket packet = new DatagramPacket(bytes, bytes.length);
        System.out.println("等待9998有内容");
        //将端口接收到的内容放进包中
        socket.receive(packet);
        //拆包得到包里收到的内容
        byte[] data = packet.getData();
        //得到数据长度
        int length = packet.getLength();
        System.out.println("这是我收到的内容："+new String(data,0,length));
    }
}
```

```java
public class Send {
    public static void main(String[] args) throws IOException {
        //监听9999端口，当有信息来到这里会被感受到
        DatagramSocket socket = new DatagramSocket(9999);
        byte[] bytes="Hello".getBytes();
        //包内有目的地的地址以及要发送的内容
        DatagramPacket packet = new DatagramPacket(bytes, bytes.length, InetAddress.getByName("捉妖龙"), 9998);
        //将内容发送至指定地址
        socket.send(packet);
        socket.close();
        System.out.println("发送完毕！");
    }
}
```

# 44. 反射 #

> 所谓反射，即通过配置文件中的类路径还原出类对象，并根据此对象来还原类的方法、变量

```java
//入门样例
public static void main(String[] args) {
        try {
            Properties properties = new Properties();
            //读取目标地址的文件
            properties.load(new FileInputStream("src\\re.properties"));
            String path = properties.getProperty("path");//类路径
            String method = properties.getProperty("method");//方法名
            Class cls = Class.forName(path); //得到路径中的类
            Object o = cls.newInstance(); //得到一个对象实例，但是实际上是Object
            Method method1 = cls.getMethod(method);//得到这个类的指定方法
            method1.invoke(o);//将方法注入到对象实例中，即对象调用hi()方法
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

## 原理 ##

+ 反射机制允许程序在执行期间借助于`ReflectionAPI`取得任何类的内部信息(比如成员变量、构造器、成员方法等)，并能操作对象的属性及方法
+ **==加载完类之后，在堆中就产生了一个Class类型的对象(一个类只有一个Class对象)，这个对象包含了类的完整结构信息。通过这个对象【Class】得到类的结构，这个对象就像一面镜子，透过这个镜子看到类的结构，所以称之为反射==**

![21](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171915764.png)

## 可完成哪些工作？ ##

1. 在运行时判断任意一个对象所属的类
2. 在运行时构造任意一个类的对象
3. 在运行时得到任意一个类所具有的的成员变量与方法
4. 在运行时调用任意一个对象的成员变量和方法

### 反射相关主要类 ###

1. java.lang.Class：代表一个类，**Class对象表示某个类加载后在堆中的对象**

   ```java
   Class cls = Class.forName(path); //得到path路径中的类加载后在堆中的对象

2. java.lang.reflect.Method：代表类的方法**Method对象表示某个类的方法**

   ```java
   Method method1 = cls.getMethod(method);//得到堆中类的指定方法
   ```

3. java.lang.reflect.Field：代表类的成员变量，**Field对象表示某个类的成员变量**

   ```java
   //得到类中的name属性对象
   Field name = cls.getField("name"); //只能得到公有属性
   //若要真正拿到属性的值，还需要反向绑定类的一个实例o，调用get方法得到
   String realName=(String) name.get(o);

4. java.lang.reflect.Constructor：代表类的构造方法，**Constuctor对象表示构造器**

   ```java
   Constructor constructor = cls.getConstructor();//得到类一个无参构造函数
   Constructor constructor1 = cls.getConstructor(String.class);//得到一个参数为String的构造函数
   ```

### 优点 ###

> 可以动态的创建和使用对象(这是框架底层核心，使用灵活，没有反射机制的话框架技术就失去底层支撑

### 缺点 ###

> 使用反射基本是解释执行，对执行速度有影响

#### 优化 ####

+ Method、Field和Constructor对象都是AccessibleObject对象的子类，共有setAccessible()方法
+ setAccessible()作用是启动和禁用访问安全检查的开关
+ **参数为true**表示反射的对象在使用时取消访问检查，**提高反射效率**；参数值为false表示反射的对象执行访问检查

## Class类 ##

+ Class也是类，因此也继承Object类
+ Class类对象不是new出来的，而是系统创建的
+ 对于**某个类的Class类对象，在内存中只有一份**，因为类只加载一次
+ **每个类的实例都会记得自己是由哪个Class实例所生成【每个类对应有一个Class对象】**
+ 通过Class对象可以完整地得到一个类的完整结构【通过一系列API】
+ Class对象是存放在堆的
+ 类的字节码二进制数据是放在方法区的，有的地方称为类的元数据【包括方法代码、变量名、方法名、访问权限等】

### 常用方法 ###

```java
ststic Class forName(String name) //返回指定类名name的Class对象
Object newInstance() //调用缺省构造函数，返回该Class对象的一个实例
ClassLoader getClassLoader()//返回该类的类加载器
Method getMethod(String name)//返回一个Method对象，方法名为name
```

![22](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171915097.png)

### 获取Class对象的六种方法 ###

1. **前提：**已知一个类的全类名，且该类在类路径下

   通过Class类的静态方法**foaName()获取**【若路径错误则会抛出ClassNotFoundException异常】

   **应用场景：**多用于配置文件，读取类全路径，加载类

2. **前提：**已知具体的类

   通过类的class获取，该方式最为安全可靠，程序性能最高

   ```java
   Class cls=Cat.class //得到Cat类对应的Class对象
   ```

   **应用场景：**多用于参数传递，比如通过反射得到对应构造器对象

3. **前提：**已知某个类的实例【**执行类型是被加载的Class**】，调用该实例的getClass()方法获取Class对象

   **应用场景：**通过创建好的对象，获取Class对象

4. 通过类加载器获取

   ```java
   ClassLoader cl=对象.getClass().getClassLoader();
   Class c=cl.loadClass("类的全类名");
   ```

5. 基本数据类型按如下方式得到Class类

   ```java
   Class cls=基本数据类型.class //会被自动装箱
   ```

6. 基本数据类型对应的包装类，可以通过.TYPE得到Class类对象

   ```java
   Class cls=包装类.TYPE

### 哪些类型有Class对象 ###

```java
外部类、成员内部类、静态内部类、局部内部类、匿名内部类
interface 接口；数组；enum 枚举；annotation 注解；基本数据类型；void
```

## 类加载 ##

+ **静态加载：**编译时加载相关的类，如果这个类不存在则会报错，依赖性强

  **静态加载时机**：①创建对象时(new)；②子类被加载时，父类也加载 ③调用类中的静态成员

+ **动态加载：**运行时加载需要的类，如果运行时不用该类，即使不存在该类，则不报错，降低了依赖性

  **动态加载时机：**通过反射的加载

  ```java
  int n=1;
  if(n==100){
      Class person=Class.forName("Person");
  }
  //这段编译时可以通过的，因为动态加载是只有运行到代码处才进行加载。此段代码不会运行到动态加载处，因此编译没有问题

![23](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171915486.png)

![24](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916228.png)

+ **加载和连接阶段都是由JVM虚拟机完成，初始化则是程序员指定的一些静态代码块或者静态变量的初始化**

### 加载阶段 ###

> `JVM`在该阶段的主要目的是将字节码从不同的数据源(可能是`class`文件、也可能是`jar`包，甚至网络)转化为二进制字节流加载到内存中并生成一个代表该类的`java.lang.Class`对象

### 连接阶段 ###

#### 验证 ####

1. **目的是为了确保Class文件的字节流中包含的信息符合当前虚拟机的要求**，并且不会危害虚拟机自身的安全
2. 验证内容包括文件格式验证(二进制文件是否以为0xCAFE BABE开头)、元数据验证、字节码验证和符号引用验证
3. 可以考虑使用`Xverify:none`参数来关闭大部分类验证措施，缩短虚拟机类加载时间

#### 准备 ####

JVM虚拟机会在该阶段**对静态==变量==分配内存并默认初始化(对应数据类型的默认初始值**，如0，null，false等)。这些变量所使用的的内存都将在方法区中进行分配

#### 解析 ####

虚拟机将常量池内的符号引用替换为直接引用的过程【可以理解原先没有具体地址，放在内存中后就有了具体地址】

### 初始化 ###

1. 到初始化阶段，才真正开始执行类中定义的Java程序代码，此阶段是执行`<client>()`方法的过程
2. `<client>()`方法是由编译器**按语句在源文件中出现的顺序，依次自动收集类中的所有静态变量赋值动作和静态代码块中的语句，并进行合并【只会在首次类加载时执行一次】**
3. 虚拟机会保证一个类的`<client>()`方法**在多线程环境中被正确地加锁、同步【这是内存中同一对象只有一个Class的原因】**，如果多个线程同时去初始化一个类，那么只会有一个线程去执行这个类的`<client>()`方法，其他线程都需要阻塞等待，知道活动线程执行`<client>()`方法完毕

## Field类 ##

> Class中的成员变量取出来后存放在Field类中

### 常用方法 ###

![25](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916240.png)

![26](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916898.png)

## Method类 ##

> Class中的方法取出来后存放在Method类中

### 常用方法 ###

![27](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916151.png)

![28](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916094.png)

## Constructor类 ##

> Class中的构造器取出来后存放在Constructor类中

### 常用方法 ###

![29](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916948.png)

## 通过反射创建对象 ##

![30](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916842.png)

+ 暴破即暴力破解的意思，设置为`true`后可以利用私有构造器构造成员

# 45. JDBC #

**知识储备**：[韩顺平老师零基础Mysql配套笔记](https://blog.csdn.net/weixin_45488428/article/details/123257085?spm=1001.2014.3001.5501)

> JDBC为访问不同的数据库提供了统一的接口，为使用者屏蔽了细节问题

![31](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916667.png)

## 获取数据库连接5种方式 ##

### 1. 静态加载驱动 ###

```java
			//获得一个驱动类
			Driver driver = new Driver();
            String url="jdbc:mysql://localhost:3306/experiment";
            //将数据库账号密码放入properties中
            Properties properties = new Properties();
            properties.setProperty("user","root");
            properties.setProperty("password","admin");
            //驱动通过账号密码以及端口位置获得一个连接
            Connection con = driver.connect(url, properties);
```

### 2. 动态加载驱动 ###

```java
			//利用反射加载驱动类
            Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
			//利用反射创建一个驱动实例
            Driver driver = (Driver)aClass.newInstance();
			//后续步骤一致
```

### 3. 通过DriverManager控制驱动 ###

```java
			//加载驱动类
            Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
            Driver driver = (Driver)aClass.newInstance();
            String url="jdbc:mysql://localhost:3306/experiment";
            DriverManager.registerDriver(driver); //加载驱动
            //根据路径和账号得到连接
            Connection con = DriverManager.getConnection(url, "root", "admin");
            
```

### 4. 加载驱动类时加载驱动 ###

```java
			//加载驱动类，源码中静态代码块会调用DriverManager完成驱动的注册
            Class.forName("com.mysql.jdbc.Driver");
            String url="jdbc:mysql://localhost:3306/experiment";
            //驱动注册后，根据路径和账号得到连接
            Connection con = DriverManager.getConnection(url, "root", "admin");
```

+ mysql驱动5.1.6以上可以无需`Class.forName("com.mysql.jdbc.Driver")`
+ 从jdk1.5以后使用了jdbc4，不再需要显示调用class.forName()注册驱动，而是会自动调用驱动jar包下META-INF\java.sql.Driver文本中的类名去注册
+ **建议还是写上Class.forName("com.mysql.jdbc.Driver")**，更加明确

### 5. 利用Properties保存数据 ###

> 连接前将关键数据放入properties，这样灵活性更强

```java
			Properties properties = new Properties();
            properties.load(new FileInputStream("src\\mysql.properties"));
            String user = properties.getProperty("user");
            String password = properties.getProperty("password");
            String driver = properties.getProperty("driver");
            String url = properties.getProperty("url");
            Class.forName(driver);
            //根据路径和账号得到连接
            Connection con = DriverManager.getConnection(url, user, password);      
```

## ResultSet结果集 ##

+ 表示数据库结果集的数据表，通常通过执行查询数据库的语句生成
+ ResultSet对象保持一个光标指向其当前的数据行。**初始光标处于第一行之前【处于-1位置】**
+ **next()方法将光标移动到下一行**，并且由于在ResultSer对象中没有更多行时返回false，因此可以在while循环中使用循环来遍历结果集
+ **ResultSet实则是一个接口**，因此其只能接受实现了其接口方法的对象【java厂商制定接口标准，数据库厂商负责实现】

## Statement ##

+ **Statement对象用于执行静态SQL语句并返回其生成结果的对象**

  ```java
  con.createStatement();//获得一个实现Statement接口的对象
  ```

+ 在连接建立后，需要对数据库进行访问，执行命名或SQL语句，可以通过以下三种方式：

  + Statement
  + PreparedStatement
  + CallableStatement

+ Statement对象执行SQL语句存在SQL注入风险

  SQL注入是利用某些系统没有对用户输入的数据进行充分检查，而在用户输入数据中注入非法SQL语句段或命令，恶意攻击数据库【**因为Statement在java程序中是拼接完成的，因此若用户在输入时恶意输入一些关键字，那么就可能会造成与开发者意愿不同的结果**】

  要防范SQL注入，只要**用PreparedStatement取代Statement**就行了

### PreparedStatement ###

+ PreparedStatement执行的SQL语句中的参数用问号`?`来表示，调用PreparedStatement对象的setXxx()方法来设置这些参数

  **setXxx()方法有两个参数，第一个参数是要设置的SQL语句中的参数的索引(从1开始)，第二个参数是设置的SQL语句中的参数值**

+ **==调用executeQuery()，返回ResultSet()对象==**

+ ==**调用executeUpdate()，执行更新、包括增，删，改**==

```java
int id=5;
String name="kkk";
String sql="insert into a values (?,?)"; 
//通过连接实现PreparedStatement接口，使preparedStatement与sql语句关联
PreparedStatement preparedStatement = con.prepareStatement(sql);
preparedStatement.setInt(1,id); //第一个问号放个整型数据id
preparedStatement.setString(2,name);//第二个问号放字符串数据name
preparedStatement.executeUpdate();//执行这条更新语句
```

## JDBC的API ##

![32](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916077.png)

![33](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171916903.png)

## 事务 ##

+ JDBC程序中当一个Connection对象创建时，**默认情况下时自动提交事务**：每次执行一个SQL语句时，如果执行成功，就会向数据库自动提交而不能回滚
+ JDBC程序中为了让多个SQL语句作为一个整体执行，需要使用事务
+ **调用Connection的setAutoCommit(false)可以取消自动提交事务**
+ 在**所有的SQL语句都成功执行后，调用Connection的commit()方法提交事务**
+ 在其中某个操作失败或出现异常时，调用Connection的**rollback()方法回滚事务**

```java
try {
            con = JDBCUtils.getCon();
            con.setAutoCommit(false);//取消事务自动提交
            PreparedStatement preparedStatement = con.prepareStatement("insert into a values (5,'ppp')");
            preparedStatement.executeUpdate();
            preparedStatement=con.prepareStatement("insert into a values (9,'mmm')");
            int i=5/0; //此处会发生错误
            preparedStatement.executeUpdate();
            con.commit(); //若未发生错误，在此事务提交
            con.close();
            System.out.println("成功");
        } catch (Exception e) { 
            con.rollback(); //事务回滚到执行前
            e.printStackTrace();
        }
```

## 批处理 ##

+ 当需要成批插入或更新记录时，可以采用Java的批量更新机制，这一机制允许多条语句一次性提交给数据库批量处理。通常情况下比单独提交处理更有效率

+ JDBC批量处理语句包括下面方法：

  **addBatch()：添加需要批量处理的SQL语句或参数**

  **executeBatch()：执行批量处理语句**

  **clearBatch()：情况批处理包的语句**

+ JDBC连接MYSQL时，如果要使用批处理功能，请再url中加参数`?rewriteBatchedStatement=true`

+ 批处理往往和PrepareStatement一起搭配使用，可以既减少编译次数，又减少运行次数，效率大大提高

```java
con = JDBCUtils.getCon();
String sql="insert into a values (?,?)";
PreparedStatement preparedStatement = con.prepareStatement(sql);
for (int i=0;i<10;i++){
    preparedStatement.setInt(1,i);
    preparedStatement.setString(2,i+1+"");
    preparedStatement.addBatch();//将这条SQL加入待处理队列
    if ((i+1)%5==0){
    //执行队列中的语句
    preparedStatement.executeBatch();
    //清空库存
    preparedStatement.clearBatch();
                }
            }
    JDBCUtils.Close(con,preparedStatement,null);
    System.out.println("成功");
```

# 46. 数据库连接池技术 #

## 为什么需要连接池？ ##

+ 传统的JDBC数据库连接使用DriverManager来获取，每次向数据库建立连接时都要将Connection加载到内存中，再验证IP地址，用户名和密码【耗时0.05s~1s】。需要数据库连接的时就向数据库要求一个连接，**频繁的进行数据库连接操作将占用很多的系统资源，容易造成服务器崩溃**
+ 每一次数据库连接使用完后都要断开，如果程序出现异常而未能关闭，将导致数据库内存泄露，最终将导致重启数据库
+ 传统获取连接的方式，不能控制创建的连接数量，**如果连接过多，也可能导致内存泄露，MYSQL崩溃**
+ **==解决传统开发中的数据库连接问题，可以采用数据库连接池技术【connection pool】==**

## 介绍 ##

+ 预先在缓冲池中放入一定数量的连接，**当需要建立数据库连接时，只需从"缓冲池"中取出一个，使用完毕后再放回去**
+ 数据库连接池负责分配、管理和释放数据库连接，**它允许应用程序重复使用一个现有的数据库连接而不是重新建立一个**
+ **==当应用程序向连接池请求的连接数超过最大连接数量时，这些请求将被加入到等待队列中==**

## 种类 ##

JDBC的数据库连接池使用`javax.sql.DataSource`来表示，**DataSource只是一个接口，该接口通常由第三方提供实现**

+ **C3P0：**数据库连接池，速度相对较慢，稳定性不错

+ **Druid：**阿里提供的数据库连接池，集DBCP、C3P0、Proxool优点于一身的数据库连接池

+ **==第三方提供实现的方式是导入对应jar包，大部分jar包在网上都可以找到；若比较懒，希望jar包能够自动载入则需要为IDEA安插Maven，此后只需要特定语句即可在镜像服务器中将对应jar包下载到本地==**

  [IDEA导入Maven指南](https://blog.csdn.net/weixin_45488428/article/details/123614966?spm=1001.2014.3001.5502)，**如果只是普通Java项目则不需要部署服务器，也不需要创建Java Web项目**

### C3P0 ###

> 使用前要导入`c3p0-0.9.5.5.jar`和`mchange-commons-java-0.2.19.jar`两个jar包

````java
		//创建一个数据源对象
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();
        Properties properties = new Properties();
        properties.load(new FileInputStream("src\\mysql.properties"));
        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String driver = properties.getProperty("driver");
        String url = properties.getProperty("url");
        //连接池驱动
        comboPooledDataSource.setDriverClass(driver);
        //连接的用户账号
        comboPooledDataSource.setUser(user);
        //连接的用户密码
        comboPooledDataSource.setPassword(password);
        //连接的数据库与IP地址
        comboPooledDataSource.setJdbcUrl(url);
        //连接池初始连接量
        comboPooledDataSource.setInitialPoolSize(10);
        //连接池最大连接量
        comboPooledDataSource.setMaxPoolSize(20);
        //从连接池中拿一个连接
        Connection connection = comboPooledDataSource.getConnection();
        System.out.println(connection);
````

#### 利用配置文件进行连接 ####

> 首先导入`c3p0-config.xml`配置文件【一定放在src目录下】，配置文件内配置相关信息

```java
//直接根据配置文件中的数据库池名称，创建一个数据源对象并进行连接
ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource("myPool");
```

### Druid ###

> 使用前先导入`druid-1.1.22.jar`和`druid.properties`【用于配置信息】

```java
Properties properties = new Properties();
properties.load(new FileInputStream("src\\druid.properties"));
//通过配置文件返回数据源对象
DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
//得到一个连接
Connection connection = dataSource.getConnection();
```

#### druid.properties ####

```properties
driverClassName=com.mysql.cj.jdbc.Driver
#URL连接数据库的URL，后面的参数可不改但不删
url=jdbc:mysql://localhost:3306/experiment?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
characterEncoding=utf-8
#安装mysql时候设置的用户与密码
username=root
password=admin
#初始化物理连接的个数
initialSize=5
#最大连接池数量
maxActive=10
#获取连接时最大等待时间
maxWait=3000
#用来检测连接是否有效的sql
validationQuery=SELECT 1
#保证安全性！
testWhileIdle=true
```

# 47. DBUtils #

> 第三方用于将结果集封装成对象的工具
>
> 使用前要导入`commons-beanutils-1.8.0.jar`与`commons-dbutils-1.7.jar`两个jar包

![0](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171918241.png)

## 查询 ##

```java
		Connection con = JDBCUtils.getCon();
        QueryRunner queryRunner = new QueryRunner();//得到工具
        String sql="select * from team where id < ?";//SQL语句
		/**
         * con 连接
         * new BeanListHandler<>(xxx.class)：查询结果与哪个类对应
         * 5：占位符【问号？】数值，有多个占位符就填多个参数
         * list：将查询结果封装在链表中
         */
List<Team> list = queryRunner.query(con, sql, new BeanListHandler<>(Team.class), 5);
//如果返回结果只有一行记录，可以使用new BeanHandler<>(xxx.class)
```

```java
		//返回单行单列结果用new ScalarHandler<>()，返回的结果会被放在Object对象中
		Connection con = JDBCUtils.getCon();
        QueryRunner queryRunner = new QueryRunner();
        String sql="select name from team where id = 1";
		//单行单列结果
        Object query = queryRunner.query(con, sql, new ScalarHandler<>());
```

## 增删改 ##

```java
//利用update(con,sql,占位符值)的方式完成
QueryRunner queryRunner = new QueryRunner();
String sql="update team set name =? where id=? ";
queryRunner.update(con,sql,"Worrier",1); //传入参数，完成更新，返回值为影响的行数
```

# 48. 正则表达式 #

> 正则表达式就是用某种模式去匹配字符串的一个公式

```java
String s="1988a s d a s d a strawberry2000";
String regular="(\\d\\d)(\\d\\d)";
//采取何种规则进行匹配
Pattern pattern = Pattern.compile(regular);
//将匹配结果用matcher保存
Matcher matcher = pattern.matcher(s);
//如果有符合匹配的结果就返回true，并记录匹配位置的始末下标到groups[]
while (matcher.find()){
//得到当前匹配到的字符串
System.out.println("找到："+matcher.group(0));
//得到当前匹配到的第一个括号的字符串
System.out.println("第一个括号内找到："+matcher.group(1));
//得到当前匹配到的第二个括号的字符串
System.out.println("第二个括号内找到："+matcher.group(2));
}
```

## 底层分析 ##

+ `Pattern.compile(regular)`用来得到一个正则表达式对象，确定匹配规则
+ `pattern.matcher(s)`将匹配规则与字符串进行配对，返回Matcher对象，准备阶段至此完毕
+ `matcher.find()`开始正式匹配，**找到一个匹配的字符串就将其初始索引放入底层group[0]，将终止索引+1放入到group[1]**；若正则表达式中**==若有括号分截==，则group[2]会放匹配到第一个括号内容字符串的起始索引，group[3]会放匹配到第一个括号内容字符串的终止索引+1**；以此类推...
+ `matcher.group(index)`用于最终截取获得匹配到的字符串，**`index=0`表示输出底层group[0]`~`[1]记录的字符串下标对应的字符串【即整个匹配规则匹配到的字符串】；`index=1`则表示输出底层group[2]`~`[3]记录的字符串下标对应的字符串【即第一个括号规则匹配到的字符串】**……根据需要取出即可

## 语法 ##

### 元字符 ###

**转义符\\\：**在使用正则表达式检索某些特殊字符的时候，需要用到转义符号==【让特殊符号失去作用，成为一个单独字符串】==，否则检索不到结果，甚至报错【Java正则表达式中两个`\`代表其他语言中一个`\`】

#### 1. 限定符 ####

![34](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171918366.png)

![35](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171918915.png)

+ **Java是贪婪匹配，尽量匹配多的**

#### 2. 选择匹配符 ####

![36](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171919061.png)

#### 3. 分组组合和反向引用符 ####

#### 4. 特殊字符 ####

#### 5. 字符匹配符 ####

![37](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171919482.png)

![38](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171919327.png)

```java
\\s: 匹配任何空白字符
\\S：匹配任何非空白字符
.：匹配除\n外的所有字符，如果要匹配"."本身，则要用转义字符\\.
```

![39](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171919557.png)

#### 6. 定位符 ####

![40](https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202211171919805.png)

`正则表达式未完待续……`

# 49. 函数式编程

> JDK8之后的新特性

**为什么学？**

+ 能够看懂别人代码
+ 大数量下处理集合效率高
+ 代码可读性高
+ 消灭嵌套地狱

## 1. Lambda表达式

使用Lambda表达式让我们不用关注具体对象，而只关注对数据进行了何种操作

基本格式

```java
(参数列表)->{代码}
```



### 1.1 用例

> 当**接口仅有一个待实现方法**时，可以使用Lambda表达式，()内是方法的参数，{}是具体实现

```java
public class Main {

	public static void main(String[] args)  {
        // 传统使用匿名内部类实现接口的run方法
		new Thread(new Runnable() {
			@Override
			public void run() {
				System.out.println("线程1开启");
			}
		}).start();
        // 使用Lambda实现方法
		new Thread(()->{System.out.println("线程2开启");}) .start();
	}
	
}
```

### 1.2 省略规则

1. 参数类型可以省略
2. 方法体只有一句代码时大括号return和唯一一句代码的分号可以省略
3. 方法只有一个参数时小括号可以省略

## 2. Stream流

Stream流用于对集合进行操作，JDK内部提供的方法使得可以快速过滤集合元素，通常配合Lambda表达式一起使用

### 2.1 创建流

**单列集合**：`集合对象.stream()`【`parallelStream()`得到并行流】

```java
List<Author> authors = getAuthors();
// 得到List集合的流形式
Stream<Author> stream = authors.stream();
```

**数组**：`Arrays.stream(数组)`或`Stream.of(数组)`

```java
Integer arr[] = {1,2,3};
Stream<Integer> stream = Arrays.stream(arr);
Stream<Integer> streams = Stream.of(arr);
```

**双列集合**：转换成单列集合后再创建

```java
Map<String,Integer> map = new HashMap<>();
Stream<Map.Entry<String,Integer>> stream = map.entrySet().stream();
```

### 2.2 中间操作

> 得到流之后对流进行处理的操作，**中间操作后必须配合终结操作【2.3👇】才能最终发挥作用**

### filter 

> 对流中的元素进行条件过滤，符合过滤条件的才能继续留在流中

```java
authors.stream()
    	// author姓名长度大于1才被留下
       .filter(author -> author.getName().length()>1);
```

### map

> 对流中的元素进行计算或转换

```java
authors.stream()
    	// 只保留author中的name
       .map(author -> author.getName());
```

### distinct

> 去除流中的重复元素

**注意：distinct方法是依赖Object的equals方法来判断是否是相同对象的，所以需要注意重写equals方法**

```java
// 使用lombok依赖，在实体类中使用@EqualsAndHashCode注解即完成equals方法的重写
authors.stream()
    	// 键值完全相同的实体类只保留一份
       .distinct();
```

### sorted

> 对流中的元素进行排序

```java
// 当调用空参的sorted对实体类进行排序时，需要确保实体类已经实现了Comparable接口
// 自定义排序
authors.stream()
    	// author年龄从大到小排序
       .sorted((o1,o2)->o2.getAge()-o1.getAge());
```

### limit

> 设置流的最大长度，超出部分被抛弃

```java
authors.stream()
    	// 保留前两条数据
       .limit(2);
```

### skip

> 跳过流中的前 n 个元素

```java
authors.stream()
    	// 跳过前两条数据
       .skip(2);
```

### flatMap

> map只能把一个对象转换成另一个对象【一对一】作为流中的元素，而flatMap可以把一个对象转换为多个对象【一对多】作为流中的元素

```java
authors.stream()
    	// 得到的是List集合
       .map(author->author.getBooks());
authors.stream()
    	// 得到的是每个author对应的多个Book对象
       .flatMap(author->author.getBooks().stream());
```

## 3. 终结操作

> 经过中间操作后的数据流将以何种方式进行呈现，即为终结操作的任务

### forEach

> 对流中的元素进行遍历操作，通过传入参数指定对遍历到的元素进行什么具体操作

```java
authors.stream()
    	// 输出每个author的姓名
       .forEach(author->System.out.println(author.getName()));
```

### count

> 用于获取当前流中元素的个数

```java
authors.stream()
    	// 统计元素个数
       .count();
```

### max/min

> 获取流元素中的最大/最小值【自定义排序规则】

```java
authors.stream()
    	// author年龄从大到小排序，取出年龄最大的author
       .max((o1,o2)->o2.getAge()-o1.getAge()).get();
```

### collect

> 将流元素转换为集合

**转换为List格式**

```java
List<String> list = authors.stream()
       .map(author -> author.getName())
       // author对应的name转换为List，toSet()转换为集合
       .collect(Collectors.toList());
```

**转换为Set格式**

```java
List<String> list = authors.stream()
       .map(author -> author.getName())
       .collect(Collectors.toSet());
```

**转换为Map格式**

```java
List<String> list = authors.stream()
       .map(author -> author.getName())
       // 指定key和value
       .collect(Collectors.toMap(author->author.getName(),author->author.getBooks()));
```

### anyMatch/allMatch/noneMatch

> anyMatch用于判断是否有**任意符合**匹配条件的元素；allMatch用于判断是否**都符合**匹配条件；noneMatch用于判断是否**都不符合**条件，返回类型为boolean

```java
boolean flag = authors.stream()
       // 存在age>20的author则返回true
       .anyMatch(author->author.getAge()>20);
```

### findAny/findFirst

> 获取流中任意一个/第一个元素

```java
// 得到的是Optional对象，后续有专题讲解
authors.stream()
       // 返回随机author【不保证是第一个】
       .findAny();
```

### reduce

> 对流中的数据按照指定计算方式得到一个结果

```java
Integer sum = authors.stream()
       // 将author映射为age格式
       .map(author->author.getAge())
       // 初始result值为0，累加所有的ele【即age】
       // 不填初始值时默认第一个元素为初始值
       .reduce(0,(result,element) -> result+element );
```

## 4. Stream流特性

+ 惰性求值（如果没有终结操作，中间操作是不会得到执行的）
+ 流是一次性的（一旦一个流对象经过一个终结操作后，这个流就不能再被使用）
+ 不会影响原数据（我们在流中可以多数据做很多处理，正常情况下不会影响原来集合中的元素的）

## 5. Optional类

用于减少空指针判断语句【实现优雅判断】，函数式编程相关API使用了大量Optional

一般使用**Optional的静态方法ofNullable**来把数据封装成一个Optional对象，无论传入参数是否为null都不会出现问题

```java
Author author = getAuthor();
// 将实体类放置入Optional处理
Optional<Author> authorOptional = Optional.ofNullable(author);

```

在实际开发中我们的数据很多是从数据库获取的。Mybatis从3.5版本可以也已经支持Optional了。我们可以直接把dao方法的返回值类型定义成Optional类型，MyBastis会自己把数据封装成Optional对象返回，封装的过程也不需要我们自己操作。

**安全消费**

```java
// ifPresent判断其内封装的数据是否为空，不为空时才会执行具体的消费代码
// 优雅避免空指针异常
authorOptional.ifPresent(author1 -> System.out.println(author.getName()));
```

**获取值**

如果我们想获取值自己进行处理可以使用 get 方法获取，但是不推荐，因为当 Optional 内部的数据为空的时候会出现异常，可以使用 orElseGet 方法设置默认值避免此类问题

```java
// 当值为空时返回新创建的Author对象，否则返回原数据
authorOptional.orElseGet(() -> new Author());
// 当值为空时主动抛出异常
authorOptional.orElseThrow(() -> new RuntimeException("数据为null"));
```

**过滤**

```java
// 当存在author年龄大于88的author时进行消费
authorOptional.filter(author -> author.getAge()>88).ifPresent(author->System.out.println(author.getName()));
```

**数据转换**

```java
// 取出author对应的books，当不为空时进行消费
authorOptional.map(author -> author.getBooks()).ifPresent(books->System.out.println(books));
```

## 6. 方法引用

在使用Lambda时，如果方法体中只有一个方法调用（包括构造方法），则可以用方法引用进一步简化代码

### 推荐用法

在使用 Lambda 时不需要考虑什么时候用方法引用，用哪种方法引用，格式是什么。只需要在写完 Lambda 方法发现方法体只有一行代码，并且是方法的调用时就将其转为方法引用格式

```java
类名::方法名
```



# 设计模式 #

## 1.单例设计模式 ##

> 一个类只允许有一个实例对象【即只能new一个对象】

### ①饿汉式 ###

+ 将构造器私有化
+ 在类的内部直接创建对象【静态】
+ 提供一个公共的`static`方法返回对象

```java
//通过调用静态方法获得唯一的一个类对象
class GirlFriend{
    private static GirlFriend lhc=new GirlFriend();
    private GirlFriend(){}; //构造方法为静态，则无法在类之外new对象
    public static GirlFriend getGirlFriend(){ //设置为类方法是为了使用静态变量lhc
        return lhc;
    }
}
```

**==对象在类加载时就会被创建！==**

### ②懒汉式 ###

+ 构造器私有化
+ 提供一个公共的static方法返回对象
+ **==在调用方法时才完成对象的创建，避免浪费资源==**

```java
//通过调用静态方法获得唯一的一个类对象
class GirlFriend{
    private static GirlFriend lhc;
    private GirlFriend(){}; //构造方法为静态，则无法在类之外new对象
    public static GirlFriend getGirlFriend(){//设置为类方法是为了使用静态变量lhc
        if(lhc==null){
            lhc=new GirlFriend(); //第一次调用方法时创建对象
        }
        return lhc;
    }
}
```

### 两者区别 ###

+ 二者最主要的区别在于**创建对象的时机不同**：饿汉式是在类加载就创建了对象实例，而懒汉式是在使用时才创建
+ **饿汉式不存在线程安全问题，懒汉式存在线程安全问题**
+ 饿汉式存在浪费资源的可能。因为如果程序员一个对象实例都没有使用，那么饿汉式创建的对象就浪费了

## 2. 模板设计模式 ##

> 利用抽象类将需要完成的公共部分放在一个模板类中，而实现类只实现每个类特殊的地方
>
> 就像一个模板将大体内容固定住，只需要对其中填空【抽象方法实现】就行

```java
public class SmallChangeSys {
    public static void main(String[] args) {
        new B().test();
        new C().test();
    }
}
abstract class Template {
    public abstract void job();

    public void test() {
        System.out.println("我是公共部分");
        job(); //我是每个类的特殊部分
    }
}
class B extends Template {
    @Override
    public void job() {
        System.out.println("我是特殊部分1");
    }
}
class C extends Template {
    @Override
    public void job() {
        System.out.println("我是特殊部分2");
    }
}
```

`设计模式未完待续……`
