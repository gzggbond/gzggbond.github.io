---
title: C++常用API
date: 2024-09-03 14:27:23
excerpt: 本文总结了数据结构使用C++语言实现算法时常用的API调用方式
tags:
  - 数据结构
categories:
  - [计算机基础,理论知识,数据结构]
---

# 1. 头文件 #

> 使用内置函数时往往需要调入相应的头文件，对于新手而言可能对这些头文件不太熟悉，因此直接用一条语句将所有头文件调入即可

```c++
#include<bits/stdc++.h>
using namespace std;    // 有此语句才能使用cin和cout，可以简单这么理解
```

# 2. 输入输出 #

```c++
// 若是读取时以空格作为分隔符则使用cin<<
// 若是要读取空格，以回车作为分隔符则使用getline()
getline(cin,str);		// string类型的str接收一整行数据
cin.getline(arr,20);	// 将输入放进大小为20的char数组中
```

```c
// 若只是输出结果，无特殊格式要求，则使用cout<<
// 若输出是小数且对格式有所要求则使用printf()
// %s对应string，%c对应char,%d对应int,%f对应float,%lf对应double
double sum=1.664;
printf("sum is %06.2lf",sum);// 输出结果总共占6位[包括小数点]，其中小数占2位，不足的部分用0填充
// 最后输出sum is 001.66
```

# 3. 常用API #

## 字符串string ##

### 输入与遍历 ###

```c++
string str; 	// 定义一个字符串类型str
cin >> str;		// 接收输入的字符串，读取时跳过空格和回车，且以空格或回车符作为结束标志
getline(cin,str);	// 接收输入流，读取时不跳过空格和回车，且以回车作为结束标志
// 遍历时直接for循环用下标对str进行访问即可
```

### 字符串转十进制 ###

```c++
stringstream ss1;		// 创建一字符串流
int result;
ss1<<"17";				// 将字符串"17"放入
ss1>>result;			// 以10进制的形式解析先前放入的数据并将结果压入result中
ss1.clear();			// 初始化流
ss1<<"17";
ss1>>hex>>result;		// 以16进制的形式解析先前放入的数据并将结果压入result中
// 二进制转十进制、十进制转二进制可以用位运算和移位运算
```

### 方法 ###

```c
s.length();		// 返回字符串大小
// C++中比较两个字符串是否相等可直接用运算符 
// s的[left1,right1]与str的[left2,right2]相比较，ASK码逐个相减，
s.compare(left1,right1,str,left2,right2);	// 若不填left与right，则默认为整个字符串 
s.push_back(char);			// 在尾部插入一字符 
s.pop_back();			// 删除尾部字符
string::iterator iter=s.begin();// 得到字符串迭代器，初始时指向头部，可用下标向后访问
s.insert(iterator,char);		// 在迭代器[由begin()或end()得到]指向位置前插入一字符 
s.append(string);		// 在s后追加字符串
s.erase(iterator);	// 删除迭代器所指的字符
s.erase(int left,int length);	// 删除从下标left开始往后length个字符
s.replace(int left,int length,string target);// 将下标从left往后的length个字符替换为target
s[i]=toupper(s[i]);// 将s[i]指向的字符变大写并赋值给s[i],tolower()同理

s.find(string);	// 查找目标字符串第一次出现的下标迭代器位置
s.find(str,index);	// 从index下标开始查找字符串第一次出现的位置，找不到时返回string::npos

sort(s.begin(),s.end());	// 按照字典序进行排序，包前不包后
s.substr(int left,int length);	// 从下标left开始往后截取length个长度
```

## 栈stack和队列queue ##

```c
// 队列queue用法同栈
stack<int> s1;	// 声明一个栈
s1.empty();		// 判断是否为空
s1.pop();		// 出栈但不返回值
s1.push(int);	// 入栈
s1.size();		// 返回栈元素个数
s1.top();		// 取栈顶元素
```

## 双端队列deque

```c++
empty()
size()
front()
back()
push_front()	// 队头插入元素
push_back()		// 队尾插入元素
pop_front()		// 删除队头
pop_back()		// 删除队尾
erase(iter)		// 根据迭代器位置进行删除
// 同样有begin()，end()，rbegin()，rend()等    
deque<int>::iterator iter=deq.begin();	// 得到开头的迭代器
```

## 排序sort ##

> 排序规则返回`true`时不交换，返回`false`时交换，正好与`java`规则相反

```c
sort(arr,arr+length,less<int>());		// 对arr数组从小到大排序[默认]
sort(arr,arr+length,greater<int>());	// 从大到小排序
```

```c
// 对结构体自定义排序
typedef struct Node{
	int val;
}Node; 
// a可理解为前面的元素，b理解为后面的元素
bool compare(Node a,Node b){
    // 如果前面元素大于后面元素就不交换，所以这是从大到小排序
	return a.val>b.val;	 
}
	Node arr[3];	 
	for(int i=0;i<3;i++){
        // 没有new的用.来访问
		arr[i].val=i;//初始小到大
	}
	sort(arr,arr+3,compare);// 经过排序后变根据val从大到小
```

```c
// 对字符串自定义排序
// 字符串里的每个字符实则是char
bool compare(char a,char b){
    // a大于b时不交换，说明前面的数ASK码小，是字典逆序
	return a>b;	// 大于0不交换 
}
string s="abc";	// 初始为字典序
sort(s.begin(),s.end(),compare);	// 排序后为字典逆序
```

## 动态数组vector ##

```c++
vector<int> arr;
vector<int> arr(n,-1);	// 长度为n，初始值为-1的vector
// 当元素存在之后可直接用下标访问 

arr.begin()		// 容器第一个元素
arr.end()		// 容器最后一个元素后一个位
arr.rbegin()	// 容器最后一个元素
arr.rend()		// 容器第一个元素前一个位

arr.back();		// 返回arr的最后一个元素
arr.front();	// 返回arr的第一个元素
arr.empty();	// 返回arr是否为空
arr.pop_back();	// 删除arr的最后一个元素
arr.push_back(int);	// 在末尾增加一个元素 
arr.size();		// 返回当前元素个数 
arr.erase(arr.begin());	// 删除迭代器指向的元素直到末尾
arr.erase(left,right);	// 删除[left,right)的元素，二者都是迭代器
arr.insert(arr.begin(),5);		// 迭代器所指向的位置前插入5
// 比较两个vector是否相等可以直接使用运算符

sort(arr.begin(),arr.end()); // 进行从小到大排列，范围包前不包后,end()指向最后一个元素的后一个
reverse(arr.begin(),arr.end());	// 内容反转，范围包前不包后
```

## 优先级队列priority_queue ##

```c++
// 构造一个大顶堆，堆中小于当前节点的元素需要下沉，因此使用less
priority_queue<int, vector<int>, less<int> > queue;
// 构造一个小顶堆，堆中大于当前节点的元素需要下沉，因此使用greater
priority_queue<int, vector<int>, greater<int> > queue;

p.empty();	// 堆是否为空 
p.pop();	// 将队列头移出，但是不返回值 
p.push();	// 压入元素 
p.size();	// 返回堆大小 
p.top();	// 得到堆顶元素值 

// 重写less/greater中的optertor方法，<对应less，大于号>对应greater
bool operator < (Node node1,Node node2){
	// 优先队列排序规则和sort正好相反，为true时候交换
	// 所以这个规则出来是基于node.val的大根堆
	return node1.val < node2.val;
}
priority_queue<Node,vector<Node>,less<Node>> p;
```

## 哈希表unordered_map ##

```c++
unordered_map<int,string> map; // 创建一个[关键字，键值]为[int,string]的哈希表
map.insert(pair<int,string>(1, "tom"));// 插入映射对[1,"tom"]到哈希表中
map[i]="jack";	// 插入映射对[i,"jack"]，i可以为任何int型数据
map.count(key)  //判断map是否存在存在key值，有则返回1，反之返回0
map.size();		//返回哈希表大小
map.empty();	//判断哈希表是否为空
map.at(key);	//返回key对应的value

//删除某个映射对只能通过迭代器进行删除
//find()若找不到key值则会返回map.end()
unordered_map<int,string>::iterator iter=map.find(key);
	if(iter!=map.end()){	//说明成功找到
		map.erase(iter);	//删除此迭代器指向的映射对
	}

//遍历方式
for (auto p : map) {
	int front = p.first;   //key
    string end = p.second;   //value
}
```

## 哈希集合set ##

```c
// set内元素自动递增排序，unordered_set乱序，速度会更快
set<int> s;		// 声明一个int型的set
insert(int)		// 在集合中插入元素 
begin()			// 返回指向第一个元素的迭代器 
clear()			// 清除所有元素 
empty()			// 如果集合为空，返回true 
end()			// 返回指向最后一个元素的迭代器，此时指向空 
erase(int)		// 删除集合中的元素
find(int)		// 返回一个指向被查找到元素的迭代器
size()			// 集合中元素的数目
count(key)		// 是否存在某个元素
```

# 4. 类及传参

```c++
#include<bits/stdc++.h>
using namespace std;
typedef struct Node{
	int val;
	struct Node *next;
    // 无参构造函数
    Node(){}
    // 有参构造函数
	Node(int v){
		this->val=v;
	}
}Node;
// 传入的数组是头指针，不能在函数里计算数组长度，应在主函数算完传入
void initial_arr(int arr[],int length){
	
	for(int i=0;i<length;i++){
		arr[i]=i+1;
	}
	return;
}
// 结构体传入的是一个指针类型
void initial_node(Node *node){
	node->val=5;
}
int main(){
	// 初始化一个数组，内容初始为空，需要手动赋值
	int arr[6];
	initial_arr(arr,sizeof(arr)/sizeof(int));
	Node *node=new Node(2);
	initial_node(node);
	cout<<node->val;
}
```

```c++
typedef struct Node{
	int val;
	struct Node *next;
}Node; 
	
int main(){
    // 节点数组
	Node *nodes[5];
	for(int i=0;i<5;i++){
		nodes[i]=new Node;
		nodes[i]->val=i+1;
	}
	nodes[0]->next=nodes[1];
	cout<<nodes[0]->next->val;
}
```

