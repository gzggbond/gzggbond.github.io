---
title: LeetCode题目笔记
date: 2024-04-13 14:27:23
excerpt: 本文总结了本人在LeetCode遇到的具有代表性的各算法题思考方向与解析，持续更新
tags:
  - 数据结构
categories:
  - [计算机基础,理论知识,数据结构]
---

# 一、哈希表 #

## 1. 两数之和:white_check_mark: ##

> 给定一个整数数组 `nums` 和一个整数目标值` target`，请你在该数组中找出和为目标值的那两个整数，并返回它们的数组下标。
>
> 你可以假设每种输入只会对应一个答案。但是，数组中**同一个元素不能使用两遍**。<br>你可以按任意顺序返回答案。

```css
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。

提示：
2 <= nums.length <= 103
-109 <= nums[i] <= 109
-109 <= target <= 109
只会存在一个有效答案
```

### 思路分析 ###

+ 寻找两数之和是否等于另一个数，即`x+y=target`，我们可以先锁定一个数`x`，再对数组进行遍历找`y`，如果找到问题就解决了
+ 但是我们发现数组的遍历效率较低，有没有一种数据结构可以实现数组这样下标和值对应起来同时效率又比较高呢？于是可以想到**哈希表**
+ 首先利用一个`for`循环将**数组下标**与**下标对应的值**映射到`Map`中，其中下标对应的值为`主键Key`
+ 再次`for`循环，当前扫描到的数组元素就是我们锁定的`x`，接着通过`y=target-x`，如果`map.containsKey(y)`为**`true`**说明在`map`里面有这么一个数字可以满足，同时`map.get(y)`为**`false`**说明我们要的值与当前扫描到的值不一致，即满足题目要求
+ 返回**`new int[]{i,map.get(y)}`**，返回这两个元素下标即最终答案

### 代码实现 ###

```java
public int[] twoSum(int[] nums, int target) {
		//用地图存放数组，查阅起来更快速
		Map<Integer, Integer> map = new HashMap<Integer,Integer>();
		for(int i=0;i<nums.length;i++) {
			map.put(nums[i], i);
		}
		for(int i=0;i<nums.length;i++) {
			//用目标数字-当前数字就是还差多少
			int remainder=target-nums[i];
			if(map.containsKey(remainder)&&map.get(remainder)!=i) {
				//如果当前地图中存在这样一个数字，并且它对应的下标与当前扫描到的数字不同
				//说明找到了
				return new int[] {i,map.get(remainder)};
			}
		}
        return null;
    }
```

# 二、模拟计算 #

## 2. 两数相加 ##

> 给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。<br>请你将两个数相加，并以相同形式返回一个表示**和**的链表。<br>你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

```css
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807

输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]

每个链表中的节点数在范围 [1, 100] 内
0 <= Node.val <= 9
题目数据保证列表表示的数字不含前导零
```

### 思路分析 ###

+ 看到加法我们就会联想到数学上的竖式运算加法，我们可以利用节点来代替竖式运算的每一位，模拟计算
+ 我们都知道，竖式加法中经常会有进位，这是这道题的难点之一。不难发现，**竖式计算中同位一次性最多进一位**，我们可以用`int flag`来表示进位情况，为`0`代表没有进位；同时结果需要接收，我们创建一个头节点`l3Head`，同时将其值赋予`l3`，`l3`是真正参与遍历的节点。
+ 首先对特殊情况进行分析：①`l1`和`l2`都空 ②`l1`为空但是`l2`不空 ③`l2`为空但是`l1`不为空
  1. 两者都为空，相加自然为空，返回值为`null`
  2. ②与③殊途同归，如果只有一者为空，说明竖式运算有一方为`0`，即返回值为不空的节点
+ 接着再对第二特殊情况进行分析：①L1位数少于L2 ②L2位数少于L1 
  1. 如果`l1`位数少于`l2`，说明在运算到一半时，`l1`会变为`null`。我们可以发现，如果当前进位符`flag==0`，说明没有其他运算了，直接把`l2`剩下的部分衔接到`l3`即可；
  2. 但是如果`flag==1`，可能后期会发生一系列运算(如`9999`)，因此我们需要对`l2`进行遍历，如果`l2.val+flag>=10`，说明还有进位，我们就取该值`%10`结果加入到`l3`中，往后继续遍历，如果一直往后都有进位，则在最后一位需要做特殊处理：手动创建一个节点`new ListNode(1)`添加到`l3`中，返回`l3Head.next`就是答案
  3. 当然，在`flag==1`时可能我们运气会好一些，即进了几位后发现`L2.val+flag<10`，则此时我们就没必要继续遍历`l2`的剩余部分，直接将剩余的整个`l2`链放到`l3`后边即可
+ 最后是常规情况，即`l1`位数等于`l2`位数
  1. 首先创建临时变量`int temp=L1.val+L2.val+flag`接收结果。如果`temp>=10`，说明有进位，此时我们取`temp%10`加入到`l3`链表中并将`flag`置`1`；如果`temp<10`，说明没有进位，我们直接将`temp`加入到`l3`链表中并将`flag`置`0`。
  2. 最后所有链表遍历完毕后，查看`flag`值，如果等于`1`，说明还有最后一个进位没有算到，需要手动创建一个节点`new ListNode(1)`添加到`l3`中，返回`l3Head.next`就是答案。

### 代码实现 ###

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
		if(l1==null) {
			return l2;
		}else if(l2==null) {
			return l1;
		}
		ListNode l3Head = new ListNode(-1); //头指针
		ListNode l3 = l3Head; //l3中实际遍历的指针
		int flag=0;  //进位符
		while(l1!=null || l2!=null) {
			if(l1==null && l2!=null) {
				if(flag==0) {
					l3.next=l2;
					return l3Head.next;
				}else if(flag==1){
					while(l2!=null) {
						int result=l2.val+flag;
						if(result>=10) {
							ListNode node = new ListNode(result%10);
							l3.next=node;
							l3=l3.next;
							l2=l2.next;
							if(l2==null) {
								l3.next=new ListNode(1);
								return l3Head.next;
							}
						}else {
							ListNode node = new ListNode(result);
							l3.next=node;
							l3=l3.next;
							l3.next=l2.next;
							return l3Head.next;
						}
					}
				}
			}else if(l2==null && l1!=null) {
				if(flag==0) {
					l3.next=l1;
					return l3Head.next;
				}else if(flag==1){
					while(l1!=null) {
						int result=l1.val+flag;
						if(result>=10) {
							ListNode node = new ListNode(result%10);
							l3.next=node;
							l3=l3.next;
							l1=l1.next;
							if(l1==null) {
								l3.next=new ListNode(1);
								return l3Head.next;
							}
						}else {
							ListNode node = new ListNode(result);
							l3.next=node;
							l3=l3.next;
							l3.next=l1.next;
							return l3Head.next;
						}
					}
				}
			}
			int temp=l1.val+l2.val+flag;
			if(temp>=10) {
				flag=1;
				temp=temp%10;
				ListNode node = new ListNode(temp);
				l3.next=node;
				l3=l3.next;
			}else if(temp<10){
				flag=0;
				ListNode node = new ListNode(temp);
				l3.next=node;
				l3=l3.next;
			}
			l1=l1.next;
			l2=l2.next;
		}
		if(flag==1) {
			l3.next=new ListNode(flag);
		}
		return l3Head.next;
	}
```

# 三、滑动窗口 #

## 3. 无重复字符的最长子串:white_check_mark: ##

> 给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

```css
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

### 思路分析 ###

+ 暴力法两层遍历是比较容易想到的，但是这方法太傻，果断排除
+ **滑动窗口**是一个不错的方法，假设我们出发点是`start`，用一个`i`去遍历字符串，用一个集合`Set`来存储当前有过的字符，用`max`存放最长字符串长度
+ 如果`i`遍历到的字符没有出现过，我们就把`i`下标对应的字符放到`set`中，并且将此时`i-start+1`与`max`对比，看看是否需要对`max`更新
+ 当我们发现`i`遍历到的字符在`Set`有出现过时，我们就让`start`往前走一步，并将`start`下标对应的字符从`set`中移除，看看此时集合中是否还存在下标`i`对应的元素，如果还是有则重复此过程……跳出这个循环后，还需要将下标`i`对应的字符加入，因为先前与它一样的字符在集合中已经被除去了。但是不需要将当前长度与`max`进行比较，因为显然当前长度时小于等于`max`的
+ 上述基本实现了算法，但是还可以进行优化。我们发现，当`集合长度+还未遍历的字符串长度<=max`时，显然也没有继续遍历下去的必要，直接返回`max`即可

### 代码实现 ###

```java
public int lengthOfLongestSubstring(String s) {
		//滑动窗口
		Set<Character> set = new HashSet<Character>();
		int max=0;
		int startIndex=0;
		for(int i=0;i<s.length();i++) {
			//集合中没有当前遍历的这个元素
			if(!set.contains(s.charAt(i))) {
				set.add(s.charAt(i));  //将当前元素加入集合中		
				//如果当前的最大子串比记录的最大值大，那就更新最大值
				max=max>=(i-startIndex+1)?max:(i-startIndex+1);
			}else {
				while(set.contains(s.charAt(i))){
					set.remove(s.charAt(startIndex));
					startIndex++;
				}
				set.add(s.charAt(i));  //将当前元素加入集合中
			}
			if(set.size()+s.length()-1-i<=max) {
				return max;
			}
		}
		return max;
	}
```

## 438. 找到字符串中所有字母异位词:white_check_mark: ##

> 给定一个字符串 `s` 和一个非空字符串 `p`，找到 `s` 中所有是 `p` 的字母异位词的子串，返回这些子串的起始索引。<br>字符串只包含小写英文字母，并且字符串 `s` 和 `p` 的长度都不超过 `20100`。
>
> 说明：
> 字母异位词指字母相同，但排列不同的字符串。
> 不考虑答案输出的顺序。

```css
输入:
s: "cbaebabacd" p: "abc"
输出:[0, 6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的字母异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的字母异位词。
```

### 思路分析 ###

+ 我们可以将`p`的各个字母数量存放在`int[26] fre`中，每个下标对应一个字母，我们可以发现，**初始值`>0`的字符是构成异构体的字符**
+ 我们利用**滑动窗口**的思想，利用`dif`记录窗口内与目标异构体还有多少个字符距离，当`dif==0`，说明当前窗口内就是一个异构体，我们就将左索引`left`放入链表中
+ 初始值左右索引都为`0`，当`right<length`时，说明遍历还未结束，因此要往后走。**如果发现`right`在的位置对应的字符在`fre`中数量大于0**，说明这是我们要的字符，因此我们准备可以扩大右边索引，在此之前要将`fre`中对应的这个字符`-1`，因为我包括了你，你需要的数量就应该减`1`，同时包括这个字符后，说明当前窗口离目标距离减了`1`，所以`dif--`。如果发现此时`dif==0`，说明包括这个字符后，当前窗口就是目标异构体，因此将左索引加入链表中
+ 如果发现`right`在的位置对应的字符在`fre`中数量`<=0`，说明当前这个字符有两种情况：①是我们要的字符，不过它数量太多了 ②不是我们要的字符
  1. 此时`left`指向的字符一定是我们要的，所以我们可以尝试让`left`往前走，因为需要的元素离开了窗口，意味着我们里目标距离变大了，所以`dif++`，同理，`left`对应字符数量应该`+1`。完成这些操作后再看看`right`对应字符的数量是否还`<=0`，是的话就继续操作……直至`left==right`或者`right`对应字符数量`>0`
  2. 当`left==right`的时，说明我这个窗口里没有装东西，如果此时`right`对应字符数量依然`<=0`，那它肯定不是我们要的字符，所以让整个窗口往前走

### 代码实现 ###

```java
public List<Integer> findAnagrams(String s, String p) {
		int[] fre = new int[26];
		// 表示窗口内相差的字符的数量
		int dif = 0;
		// fre 统计频数
		for (char c : p.toCharArray()) {
			fre[c - 'a']++; // P中的字符数量
			dif++;
		}
		int left = 0, right = 0;
		int length = s.length();
		char[] array = s.toCharArray();
		List<Integer> result = new ArrayList<>();
		while (right < length) {
			char rightChar = array[right]; // 右索引字符
			// 是p中的字符
			if (fre[rightChar - 'a'] > 0) { // 大于0说明是我们要的字符
				fre[rightChar - 'a']--; // 当前字符需要的数量-1
				dif--; // 因为是我们要的字符，所以差距会减少
				right++; // 右索引向前
				if (dif == 0) { // 差距为0时，窗口内就是符合条件的字符串
					result.add(left);
				}
			} else {
				while (fre[array[right] - 'a'] <= 0 && left < right) { // 当前字符数量小于等于0，说明这个字符我们不需要
					fre[array[left] - 'a']++; // 左索引的值+1，因为我们准备往前走
					left++; // 左索引++，即往前走
					dif++; // 此时左索引在的位置一定是我们需要的元素，所以往前走后，不同的数目会+1
				}
				if (left == right && fre[array[left] - 'a'] <= 0) {
					// 当前字符不是我们要的，将其放在窗口里显然不成立，所以整个窗口往前
					left++;
					right++;
				}
			}
		}
		return result;
	}
```

# 四、动态规划 #

## 5. 最长回文子串:white_check_mark: ##

> 给你一个字符串 `s`，找到 `s` 中最长的回文子串。

```css
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

### 思路分析 ###

+ 暴力算法？太垃圾了，没办法才采用这招
+ 我们可以发现回文子串的一个规律：如果有一个`回文串s`，令其头尾再加两个字符`x`和`y`，若`x==y`，那么这个新字符串`S`也是回文串。我们的思路变为判断`1`个字符的子串是不是回文子串？在这个字符左右各加一个字符后是不是回文子串？再往两边各加`1`个字符还是回文子串吗……
+ **动态规划**就是专门做这事儿的，一般动态规划就是将所有的情况记录下来，根据需要取出相关元素进行应用。在这里，我们可以设置一个`boolean`二维数组`result[n][n]`用来存放字符串是否回文的结果，其中第一维表示起始下标，第二维表示终止下标。创建一个字符串`str`用于接收结果
+ 我们设置一个二重循环，第一重是字符串长度`l`(`l`是下标，实际长度是`l+1`)，第二重是字符串下标起始位置`i`。显而易见，`i+l<n`，因为长度最大为`n`，不可越界。我们再设置一个`j=l+i`用来表示字符串终止下标，`result[i][j]`的意思为：起始下标为`i`，终止下标为`j`的子串是否为回文子串
+ 在判断过程中会出现三种情况：①`字符串长度==1` ②`字符串长度==2` ③`字符串长度>2`
  1. `字符串长度==1`毫无疑问是回文串，所以当`l==0`时，将`result[i][j]=true`
  2. `字符串长度==2`若要是回文串，则有头尾下标对应的字符相同，则有`result[i][j]=s.chatAt(i)==s.charAt(j)`
  3. `字符串长度>2`若要是回文串，则需要用到我们发现的规律：①字符串头尾相同 ②除去头尾外内部是回文串。头尾相同好判断，就是`s.chatAt(i)==s.charAt(j)`，而内部是否回文则可以利用我们事先创建好的二维数组，如果`result[i+1][j-1]==true`，就说明内部是一个回文串。同时满足这两个条件就是说明当前字符串也是回文串。
+ 如果`result[i][j]==true`且`j-i+1>str.length()`，那就让这一段距离的字符串赋值给`str`

### 代码实现 ###

```java
public String longestPalindrome(String s) {
        int n=s.length();
        //创建二维数组用于存放各点之间的回文情况
        boolean record[][]=new boolean[n][n];
        String result="";//接收结果
        for(int l=0;l<n;l++){
            //l+1表示字符串长度
            for(int i=0;i+l<n;i++){
                //j表示下标i开始，长度为l+1的字符串结束下标
                int j=i+l;
                //record[i][j]表示从下标i到j的字符串回文情况
                if(l==0){
                    //说明字符串长度为1
                    record[i][j]=true;
                }else if(l==1){
                //字符串长度为2，如果头尾两者相等说明是回文字符串
                    record[i][j]=s.charAt(i)==s.charAt(j);
                }else{
                    //字符串长度大于2
                    //如果字符串两边是相等的，且里面的也是回文串，则当前字符串的是回文子串
                    record[i][j]=s.charAt(i)==s.charAt(j)&&record[i+1][j-1];
                }
                if(record[i][j]&&l+1>result.length()){
                    result=s.substring(i,j+1);
                }
            }
        }
        return result;
    }
```

## 64. 最小路径和 ##

> 给定一个包含非负整数的 `m x n` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。<br>**说明：**每次只能向下或者向右移动一步。

```css
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

### 思路分析 ###

+ 通常面对求最短路径问题，我们通常的方法是**动态规划**
+ 由于每次只能向下或者向右移动一步，因此到达一个点`[i,j]`的最短路径应该是`min(grid[i-1][j],grid[i,j-1])+grid[i+j]`
+ 因为第一行与第一列的上元素与左元素不能同时存在，因此我们对其进行特殊处理
+ 最后**二维数组的最后一个元素即为答案**

### 代码实现 ###

```java
public int minPathSum(int[][] grid) {
		for (int i = 0; i < grid[0].length - 1; i++) {
			grid[0][i + 1] = grid[0][i] + grid[0][i + 1];
		}
		for (int i = 0; i < grid.length - 1; i++) {
			grid[i + 1][0] = grid[i][0] + grid[i + 1][0];
		}

		if (grid.length == 1) {
			return grid[0][grid[0].length - 1];
		} else if (grid[0].length == 1) {
			return grid[grid.length - 1][0];
		}
		for (int i = 1; i < grid.length; i++) {
			for (int j = 1; j < grid[0].length; j++) {
				grid[i][j] = Math.min(grid[i - 1][j], grid[i][j - 1])+grid[i][j];
			}
		}
		return grid[grid.length - 1][grid[0].length - 1];
	}
```

## 70. 爬楼梯 ##

> 假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。<br>每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
>
> **注意：**给定 *n* 是一个正整数。

```css
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1 阶 + 1 阶 + 1 阶
1 阶 + 2 阶
2 阶 + 1 阶
```

### 思路分析 ###

+ 到达的第`n`阶上一步一定是`n-1`或者`n-2`，用递归太过累赘，因此我们可以考虑**动态规划**
+ 设置一个长度为`n`的数组，来存放到达第`n`阶及之前的所有结果，第`n`阶结果为`n-1阶与n-2阶`结果之和
+ 到达题目给出的阶数后就是答案

### 代码实现 ###

```java
public int climbStairs(int n) {
        if(n==1){
            return 1;
        }
        int[] result=new int[n];
        result[0]=1;
        result[1]=2;
        for(int i=2;i<result.length;i++){
        	//每次来到楼梯的前一步肯定是前一个或者前两个
            result[i]=result[i-1]+result[i-2];
        }
        return result[n-1];
    }
```

## 96. 不同的二叉搜索树 ##

> 给定一个整数 *n*，求以 1 ... *n* 为节点组成的二叉搜索树有多少种？

```css
输入: 3
输出: 5
解释:
给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

### 思路分析 ###

+ 二叉搜索树是**左边大于根节点，右边小于根节点**的树

+ 当给定一个整数`n`时，`1~n`都有可能是根节点，因此需要每种情况都尝试一遍

+ 选定根节点后，比根节点小的才可以放在其左边；比根节点大的放在其右边。

  如果长度为`n`，根节点为`j`，则`j-1`个放在左边，`n-j`个放在右边，不同的数量就会有不同的组成方式，两者数量相乘就是`j`节点作为根节点时的搜索二叉树情况

+ **我们如何知道不同数量的节点对应的搜索二叉树组成方式有几种呢？**可以使用**动态规划**。<br>我们用**一个`G[n]`来存放`n`个节点时二叉搜索树的数量**

+ 当数量为`0`(空树)或者`1`时，显而易见此时搜索二叉树的数量为`1`，`G[0]=G[1]=1`

+ 当数量为`2`时，则有两种情况：

  ①`1`作为根节点`j`，查询有几个比它小的数可以放在左边，如果没有就是空树；查询有几个比它大的数可以放在右边，如果没有就是空树；将查询到的左右节点情况对应的二叉搜索树数量进行相乘，就是`1`作为根节点时的二叉搜索树情况

  ②`2`作为根节点，查询有几个比它小的数可以放在左边，如果没有就是空树；查询有几个比它大的数可以放在右边，如果没有就是空树；将查询到的左右节点情况对应的二叉搜索树数量进行相乘，就是`2`作为根节点时的搜索二叉树情况

  ③**两种情况之和就是`G[2]`的值，即有两个节点时搜索二叉树的数量**

+ 知道了`G[2]`，同理我们可以得到`G[3]...G[n]`，最后返回`G[n]`就是我们的正确答案。

### 代码实现 ###

```java
public int numTrees(int n) {
		//G[i]表示i个数字时会有几种二叉搜索树
		int[] G = new int[n + 1];
		//0个数字时表示空树，也是一种
		G[0] = 1;
		G[1] = 1;
		for (int i = 2; i <= n; i++) {
			//i表示有几个数且为边界
			for (int j = 1; j <= i; j++) {
				//j表示当前那个数字做根
				//如果这个数字j做根，其左边树与右边树乘积才是总数量
				G[i] = G[i] + G[j - 1] * G[i - j];
			}
		}
		return G[n];
	}
```

## 139. 单词拆分 ##

> 给定一个**非空**字符串 *s* 和一个包含**非空**单词的列表 *wordDict*，判定 *s* 是否可以被空格拆分为一个或多个在字典中出现的单词。<br>**说明：**
>
> + 拆分时可以重复使用字典中的单词。
> + 你可以假设字典中没有重复的单词。

```css
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```

### 思路分析 ###

+ **如果我们可以知道当前单词的子单词是否可以在字典中找到的话，就可以知道当前整个单词是否可以被找到**，因此考虑**动态规划**，将从字符串开头直至字符串结尾的所有子字符串是否可以被找到的情况记录在`flag[]`中
+ 辨认当前的字符串`[0,i]`是否可以被找到，只需要令`j=i-1`，用`j+1`作为下标访问`flag`，表示长度为`j`的字符串是否可被访问
+ 如果其可以被访问，则截取字符串`[j+1,i+1]`的内容查看字典，若也能被访问，则说明字符串`[0,i]`可以被访问。同时将`flag[i+1]`标记为true，说明长度为`i`的字符串可以被访问，便于下一次利用；
+ 如果其不能为访问，则`j`往前走，直至找到可以被访问的长度，再将`[j+1,i+1]`部分查看字典，若有则对`flag`做标记，如果访问到最后实在没有，则说明`[0,i]`这段字符串在字典中是找不到的。
+ 结果返回`flag[s.length]`，即长度为`s.length`的字符串是否可被访问情况

### 代码实现 ###

```java
public boolean wordBreak(String s, List<String> wordDict) {
		// 将字典转换为集合
		Set<String> set = new HashSet<String>(wordDict);
		boolean[] flag = new boolean[s.length() + 1];//记录相关字符串长度是否可以在字典找到
		//flag[x]表示从初始位置-1开始，往后数x长度的字符串是否可以被找到
		flag[0] = true; // 长度为0的字符串当然在字典可以找到
		for (int i = 0; i < s.length(); i++) { // 当前字符串下标
			for (int j = i - 1; j >= -1; j--) {
				if (flag[j + 1]) { // 下标+1将索引所在的实际长度进行表示
					if (set.contains(s.substring(j + 1, i + 1))) {
						flag[i+1] = true; //下标转实际长度
						break;
					}
				}
			}
		}
		return flag[s.length()]; //长度为s.length()的字符串是否可以被找到
	}
```

## 152. 乘积最大子数组 ##

> 给你一个整数数组 `nums` ，请你找出数组中乘积最大的**连续子数组**（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

```css
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

### 思路分析 ###

+ 如果是正数，当然是越乘越大，但是如果遇到负数的话，马上又会变得很小，而且当前是负数不代表后边是负数，当遇到偶数个负数时又会摇身一变成为很大的数字……这些是此题的难点，**如果我们能够时刻掌握在扫描到某一个元素时候的最大乘积，那当然就可以解决问题了。同时我们也需要保存扫描到当前位置时的最小元素，防止其"摇身一变"成为最大的元素**
+ 由于扫描到每个元素时的乘积最大与最小值都与当前元素的上一个元素有关联，因此**动态规划**可以帮我们达到目的
+ 我们利用一个二维数组`res[][2]`来存放当前元素的最大值与最小值，首元素的最大值与最小值都是自己。第二个元素开始，首先得到上个元素最大值与最小值与当前元素的乘积`result1和result2`以及当前扫描的元素`nums[i]`，确定三者大小关系，从而为当前元素的最大值与最小值赋值
+ `res[][0]`存放的是扫描到当前元素时的最大乘积值，因此我们每次扫描一个元素都要与`Max`进行比较，取最大值即答案

### 代码实现 ###

```java
public int maxProduct(int[] nums) {
		int[][] res = new int[nums.length][2]; // 存放每个位置的最大数和最小数
		res[0][1] = res[0][0] = nums[0]; // 第一个数字的最小数和最大数都是它本身
		int Max = nums[0];
		for (int i = 1; i < nums.length; i++) {
			int result1 = res[i - 1][0] * nums[i]; // 上一个的最大数*当前数字
			int result2 = res[i - 1][1] * nums[i]; // 上一个的最小数*当前数字
			if (result1 > result2) {
				if (result1 > nums[i]) { // 与当前扫描的数字比较
					res[i][0] = result1; // 说明result1是最大的，可以放心赋值
					if (result2 > nums[i]) {
						res[i][1] = nums[i];
					} else {
						res[i][1] = result2;
					}
				} else if (result1 <= nums[i]) {
					res[i][0] = nums[i]; // 说明nums[i]是最大的，可以放心赋值
					res[i][1] = result2; // 说明result2是最小的
				}
			} else {
				if (result2 > nums[i]) { // 与当前扫描的数字比较
					res[i][0] = result2; // 说明result2当前最大
					if (result1 > nums[i]) {
						res[i][1] = nums[i]; // 说明nums[i]是最小的
					} else {
						res[i][1] = result1; // 说明result1是最小的
					}
				} else {
					res[i][0] = nums[i];
					res[i][1] = result1;
				}
			}
			Max = Max < res[i][0] ? res[i][0] : Max;
		}
		return Max;
	}
```

## 198. 打家劫舍 ##

> 你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。
>
> 给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。**

```css
输入：[2,7,9,3,1]
输出：12
解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

### 思路分析 ###

+ 由于**金额不会出现负数，因此同样路线下，去过更多地方的小偷会得到更多的钱**。同时，对于到达一个目的地，可以从隔位到达(防止警报器)，也可以隔两位到达（隔更多的位没有意义，因为对于这个目的地，其金额还有可以提升的空间）。
+ 如果我们可以得到`[i-2]与[i-3]`位置的最大金额，那么就可以知道`[i]`位置的最大金额，由此我们可以使用**动态规划**，利用一个同样大小的数组`res`来存放打劫到每一家时可以有的最大金额
+ 数组的最后一个元素与倒数第二个元素比较，较大的即为答案

### 代码实现 ###

```java
public int rob(int[] nums) {
		if (nums.length <= 2) {
			if (nums.length == 0) {
				return 0;
			} else if (nums.length == 1) {
				return nums[0];
			}
			return nums[0] > nums[1] ? nums[0] : nums[1];
		}
		int[] res = new int[nums.length]; // 存放打劫到各家时的金额
		res[0] = nums[0];
		res[1] = nums[1];
		res[2] = nums[0] + nums[2];
		for (int i = 3; i < nums.length; i++) {
			int result1 = res[i - 2] + nums[i];
			int result2 = res[i - 3] + nums[i];
			res[i] = result1 > result2 ? result1 : result2;
		}
		int sum = res[nums.length - 1] > res[nums.length - 2] ? res[nums.length - 1] : res[nums.length - 2];
		return sum;
	}
```

## 221. 最大正方形 ##

> 在一个由 `'0'` 和 `'1'` 组成的二维矩阵内，找到只包含 `'1'` 的最大正方形，并返回其面积。

```css
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：4
```

### 思路分析 ###

+ 由于是找最大正方形，所以**暴力法**的思路是先圈出来一个二维数组能够承受的最大正方形，然后以这个大正方形为单位在二维数组内移动，每移动到一个地方就对整个正方形的内容进行遍历
+ 如果全`1`，说明存在这个一个最大正方形，返回面积即可；如果出现了`0`，说明当前圈出来的正方形不是我们要找的正方形，因此整个正方形向后移动，重复流程……若是这个大小的正方形遍历完后也找不到符合要求的，那就把边长-1，重新再找，直至找到一个符合要求的并返回
+ 我们可以模糊发现，正方形之间存在某种依赖关系，能不能使用**动态规划**呢？
+ 设有一个点`[i][j]`，我们发现，当其为`1`时，**取`[i-1][j]，[i-1][j-1]，[i][j-1]`的最小值`+1`就是以`[i][j]`为正方形右下角时正方形的最大边长**，由此可以得出关系式并实现

### 代码实现 ###

```java
public int maximalSquare(char[][] matrix) {
		int max = 0;
		boolean flag = false;
		int[][] temp = new int[matrix.length][matrix[0].length];
		for (int i = 0; i < matrix.length; i++) {
			for (int j = 0; j < matrix[0].length; j++) {
				temp[i][j] = matrix[i][j] - '0';
				if (!flag && matrix[i][j] == '1') {
					max = 1;
					flag=true;
				}
			}
		}
		
		for (int i = 1; i < matrix.length; i++) {
			for (int j = 1; j < matrix[0].length; j++) {
				if (temp[i][j] == 1) {
					temp[i][j] = judge(temp[i - 1][j], temp[i - 1][j - 1], temp[i][j - 1])+1;
					max = max > temp[i][j] ? max : temp[i][j];
				}
			}
		}
		return max*max;
	}

	public int judge(int a, int b, int c) {
		if (a <= b) {
			if (a <= c) {
				return a ;
			} else {
				return c ;
			}
		} else if (b >= c) {
			return c ;
		}
		return b ;
	}
```

## 279. 完全平方数 ##

> 给定正整数 `n`，找到若干个完全平方数（比如 `1, 4, 9, 16, ...`）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。
>
> 给你一个整数` n `，返回和为` n `的完全平方数的 最少数量 。
>
> **完全平方数** 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，`1`、`4`、`9` 和 `16` 都是完全平方数，而 `3` 和 `11` 不是。

```css
输入：n = 13		【因为1是完全平方数，因此所有正整数都可以被完全平方表示】
输出：2
解释：13 = 4 + 9
```

### 思路分析 ###

+ 类似这种累加的，我们都可以考虑**动态规划**
+ 设置一个数组`dp[n+1]`，存放的是其**下标数字对应的最少完全平方数**，易知：`dp[i]=i,i<4`
+ 接着我们继续往下走时，首先得知道当前这个元素所能容忍的最大开方`sqrt`是多少(比如：`17-->4`，`13-->3`)。元素减去这个数字`sqrt`的平方时，可以走一步就到达当前数字；同时对于小于这个最大开方的数字`j`，元素同样可以减去这个数字`j²`时，一步走到当前数字。我们只需要比较每一个可能并得出一个最小值赋值给`dp[i]`即可，即`dp[i]=Math.min(dp[i],dp[i-(j*j)]+1)`
+ 第一次比较时，`dp[i]`为0，不方便我们获得真正的数据，因此初始化`dp`时，将其填充为最大值`Arrays.fill(dp,Integer.MAX_VALUE)`

### 代码实现 ###

```java
public int numSquares(int n) {
		if (n < 4) {
			return n;
		}
		int[] dp = new int[n + 1];
		Arrays.fill(dp, Integer.MAX_VALUE);
		for (int i = 0; i < 4; i++) {
			dp[i] = i;
		}
		for (int i = 4; i < dp.length; i++) {
			int sqrt = (int) Math.sqrt(i); // 最大能接受的开方，如4->2
			for (int j = 1; j <= sqrt; j++) {
				dp[i] = Math.min(dp[i], dp[i - (j * j)] + 1);
			}
		}
		return dp[dp.length - 1];
	}
```

## 300. 最长递增子序列 ##

> 给你一个整数数组` nums` ，找到其中最长严格递增子序列的长度。
>
> 子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7] `是数组` [0,3,1,6,2,2,7]` 的子序列。

```css
输入：nums = [0,1,0,3,2,3]
输出：4
```

### 思路分析 ###

+ 这是一个找子序列的问题，我们可以使用**动态规划**，利用`dp[i]`存放`nums[i]`处的最长子序列，初始值为`1`，因为自身必然为自身子序列
+ 如果`nums[i]>nums[i-1]`，则说明此处是递增的。对于一个元素来说，左边的每一个元素都有可能是递增的，所以有`nums[i]>nums[j](j初始为i-1,j>=0;j--)`时都是递增的，此时`nums[j]`至少往后走一步就可以到达`nums[i]`
+ 因此有`i`处的最长子序列为：`dp[i]=Math.max(dp[i],dp[j]+1)`
+ 此处可以进行一个优化，当`dp[i]>j`，说明此时即使前边元素全部递增，也不会改变`dp[i]`的值，因此可以跳出循环

### 代码实现 ###

```java
public int lengthOfLIS(int[] nums) {
		int [] dp=new int[nums.length];
		Arrays.fill(dp, 1);
		int max=1;
		for(int i=1;i<dp.length;i++) {
			for(int j=i-1;j>=0;j--) {
				if(nums[j]<nums[i]) {
					dp[i]=Math.max(dp[j]+1, dp[i]);
				}
				if(dp[i]>j) {
					break;
				}
			}
			max=Math.max(dp[i], max);
		}
		return max;
	}
```

## 309. 最佳买卖股票时期含冷冻期 ##

> 给定一个整数数组，其中第 `i` 个元素代表了第 `i` 天的股票价格 。<br>设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:
>
> + 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
> + 卖出股票后，你无法在第二天买入股票 (即冷冻期为 `1` 天)。

```css
输入: [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

### 思路分析 ###

+ 通过分析可以知道，每个元素可能存在三种状态：①完全没有股票 ②有股票 ③刚刚卖出股票导致没有股票。利用这个特性，可以使用**动态规划**
+ 设置一个数组`dp[i][3]`，分别存放三种状态在`i`点时的最大收益。初始状态，`dp[0][0]=0【没股票收益自然是0】`，`dp[0][1]=-1*price【买入股票要花钱，收益为负数】`，`dp[0][2]=0【买入再卖出收益为0】`
+ 对于`d[i][0]`来说，表示当前完全没有股票，那么只有两种可能：①原先就没有股票 ②上一点刚刚卖出股票
+ 对于`d[i][1]`来说，表示手上有股票，那么只有两种可能：①原先就有股票 ②刚刚买入股票
+ 对于`d[i][2]`来说，表示当前刚刚卖出股票，那么只有一种可能：原先有股票才能卖出	
+ 由于每次都是取其中的最大收益，因此最后一点的收益肯定是最大的。所以返回`Math.max(dp[end][0],dp[end][2])`，很明显，最后一步还买入股票是会让收益下滑的，因此不对`dp[i][1]`做分析

### 代码实现 ###

```java
public int maxProfit(int[] prices) {	
		if(prices.length<=1) {
			return 0;
		}
		//dp[i][0]：在i这个位置完全没有股票时的最大收益
		//dp[i][1]：在i这个位置买了股票时的最大收益
		//dp[i][2]：在i这个位置刚刚卖出去股票时的最大收益
		int [][]dp=new int [prices.length][3];
		dp[0][0]=0;
		dp[0][1]=-1*prices[0];	//买入股票，资产为负数
		dp[0][2]=0;
		for(int i=1;i<prices.length;i++) {
			dp[i][0]=Math.max(dp[i-1][0], dp[i-1][2]); //①原先就没有股票 ②上个点刚刚卖出股票
			dp[i][1]=Math.max(dp[i-1][0]-prices[i], dp[i-1][1]);//①原先就有股票 ②刚刚买入股票
			dp[i][2]=dp[i-1][1]+prices[i];//原先有股票才能卖出	
		}
		//最后一天手上不应该有股票
		return Math.max(dp[prices.length-1][0], dp[prices.length-1][2]);
	}
```

## 322. 零钱兑换 ##

> 给定不同面额的硬币` coins` 和一个总金额 `amount`。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回` -1`。
>
> 你可以认为每种硬币的数量是无限的。

```css
输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
```

### 思路分析 ###

+ 面对这类累加求最小或者最大的，我们都可以使用**动态规划**
+ 利用`dp[i]`存放总金额为`i`时的所需的最少硬币数，如何得到这个最少硬币数呢？
+ 假设我们要去一个目标金额`i`，最少的硬币数当然是一步到达，因此我们应该想一想**如何才能一步到达呢？**
+ 我们每次可以跳跃的步数也就是硬币的面值`coins[j]`，也就是说如果目标金额`i`往后倒退`coin[j]`这么多的时，我们可以一步到达
+ 又因为硬币面值有很多，因此可以一步到达的位置也会有很多，我们对这些可以一步到达的位置所对应的最少硬币数之间进行对比，**选择最小的一个+1就是到达当前位置的最少硬币数了**

### 代码实现 ###

```java
public int coinChange(int[] coins, int amount) {
		int[] dp = new int[amount + 1]; // 存放当前价格硬币最少要几个
		Arrays.fill(dp, amount + 1); // 硬币最小为1，因此需要的硬币不可能比要求的数量还大，所以amount+1是最大值
		dp[0] = 0; // 价格0元只需要0枚硬币
		for (int i = 1; i <= amount; i++) {
			for (int j = 0; j < coins.length; j++) {
				if (coins[j] <= i) { // 当币值小于总价格时候
					dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1); // 距离一步到达的地方最少硬币数
				}
			}
		}
		return dp[amount] > amount ? -1 : dp[amount];
	}
```

## 337. 打家劫舍III ##

> 在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。
>
> 计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

```css
输入: [3,4,5,1,3,null,1]
     3
    / \
   4   5
  / \   \ 
 1   3   1
输出: 9
解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.
```

### 思路分析 ###

+ 我们可以发现，每个节点有两种状态：①被偷 ②不被偷
+ **当此节点被偷的时，其左右节点不能被偷；当此节点不被偷时，左右节点可以被偷，也可以不被偷**
+ 如果我们能知道一个节点的左右节点被偷时和不被偷时所能盗取的最高金额，那我们就可以解决问题，因此可以使用**动态规划**
+ 我们利用两个`Map<TreeNode, Integer>f和g`来存放该节点被偷时的最大金额和不被偷时的最大金额。又由于每个节点的最大金额要通过左右节点的情况才能得知，因此我们采用**后序**的方式，最底层被偷的时候自然最大金额就是自己的`val`，不被偷时的最大金额就是0
+ 当我们遍历到某个点`node`的时候，**该节点被偷时的最大值就是当前节点的值+左右节点不被偷时的最大值**；该节点不被偷时的最大值就是`Max(左节点被偷，左节点不被偷)+Max(右节点被偷，右节点不被偷)`

### 代码实现 ###

```java
	Map<TreeNode, Integer> f = new HashMap<TreeNode, Integer>();//选择该节点情况下最多的权值(钱)
	Map<TreeNode, Integer> g = new HashMap<TreeNode, Integer>();//不选择该节点情况下最多的钱

	public int rob(TreeNode root) {
		dfs(root);
		return Math.max(f.getOrDefault(root, 0), g.getOrDefault(root, 0));
	}

	public void dfs(TreeNode node) {
		if (node == null) {
			return;
		}
		dfs(node.left);
		dfs(node.right);
		//这个点选了，则说明左右两边没有选，当前的最大钱就是左右两边没选时的最大钱和当前节点的和
		f.put(node, node.val + g.getOrDefault(node.left, 0) + g.getOrDefault(node.right, 0));
		//这个点没选，说明左边两边可选可不选，它们选或不选的和的最大值就是当前点不选时的最大值
		g.put(node, Math.max(f.getOrDefault(node.left, 0), g.getOrDefault(node.left, 0))
				+ Math.max(f.getOrDefault(node.right, 0), g.getOrDefault(node.right, 0)));
	}
```

## 416. 分割等和子集 ##

> 给定一个**只包含正整数**的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。
>
> 注意:<br>①每个数组中的元素不会超过 100
> ②数组的大小不会超过 200

```css
输入: [1, 5, 11, 5]
输出: true
解释: 数组可以分割成 [1, 5, 5] 和 [11].
```

### 思路分析 ###

+ 首先可以排除集中无法分割成等和子集的情况
  1. 数组元素总和为奇数，无法分割
  2. 数组长度小于2，无法分割
  3. 数组中最大的元素大于总和的一半，说明剩下的元素小于总和的一半，必然不等，无法分割
+ 其实可以将题目的意思进行转换，所谓分成两个相同和的子集，实际上就是找到一个子集，使其和等于`sum/2`，剩下那个子集自然也会是`sum/2`，从而达到题目要求，所以我们的目标是找到一个子集使其`target=sum/2`
+ 假设我们当前正遍历到元素`nums[i]`，则如果要能到达`target`这个目标，则必须保证可以到达`target-nums[i]`，由此我们可以使用**动态规划**，利用`boolean[] dp[target]`存放是否可到达`target`，`dp[0]`为`true`
+ 我们对集合数组进行遍历，每次取出其遍历到的数字`temp`。接着在`[temp,target]`这个区间对`dp[]`进行取值，我们从后往前遍历，即"要到达`dp[target]`必须满足`dp[target-temp]`能够到达"

### 代码实现 ###

```java
public boolean canPartition(int[] nums) {
		int n = nums.length;
		if (n < 2) {
			// 长度比2小，分割必然有一个为空集，因此和是不相等的
			return false;
		}
		int sum = 0;
		int max = Integer.MIN_VALUE;
		for (int i : nums) {
			max = Math.max(max, i);
			sum = sum + i; // 得到总和为多少
		}
		if (sum % 2 == 1 || max > sum / 2) {
			return false; // 如果总和为奇数时不可能分为两个相同子集的；最大值大于一半，说明剩下的少于一半
		}
		int target = sum / 2; // 子集需要到达的目标
		boolean dp[] = new boolean[target + 1]; // dp[j] 代表当前是否有到 j 的子集
		dp[0] = true;// 空集就是到0的子集
		for (int i = 0; i < n; i++) {
			int temp = nums[i];
			for (int j = target; j >= temp; j--) {
				// 因为用或运算符，因此由后往前走，不然同个一元素会被多次使用判断
				// 逻辑关系是：前边的先决条件满足了才能有后边
				dp[j] = dp[j] | dp[j - temp];
			}
			if (dp[target] == true) {
				return true;
			}
		}
		return dp[target];
	}
```

## 494. 目标和 ##

> 给定一个非负整数数组，`a1, a2, ..., an`和一个目标数`S`。现在你有两个符号` +` 和` -`。对于数组中的任意一个整数，你都可以从 `+` 或 `-`中选择一个符号添加在前面。
>
> 返回可以使最终数组和为目标数 `S` 的所有添加符号的方法数。
>
> + 数组非空，且长度不会超过 20 。
> + 初始的数组的和不会超过 1000 。
> + 保证返回的最终结果能被 32 位整数存下。

```css
输入：nums: [1, 1, 1, 1, 1], S: 3
输出：5
解释：
-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3
一共有5种方法让最终目标和为3。
```

### 思路分析 ###

+ 由于数组和不超过1000，因此我们可以利用创建一个`dp[][2001]`存放`i`位置会出现的结果`j`情况，其中`-1~-1000`放置在`[1001~2000]`这个位置
+ 我们**利用`Set`存放当前位置`i`的前一个有哪些`j`是有出现的，即`dp[i-1][j]>0的j`。提高搜索效率**，只有知道上一次有哪些和出现，我们才能确保这次会有哪些和出现
+ 当上次的和放置在`>[1000]`的位置时，我们需要**对这个数字下标-1000再取反还原为真正的负数**，这个数字与当前扫描到的数字相加，根据一些正数负数的情况做一些适当处理，同时需要更新`Set`

### 代码实现 ###

```java
public int findTargetSumWays(int[] nums, int S) {
		if (S > 1000) { // 题目规定总和不超过1000
			return 0;
		}
		int n = nums.length;
		int[][] dp = new int[n][2001]; // 0~1000表示和为0到1000的个数，1001~2000表示和为-1到-1000的个数
		Set<Integer> set = new HashSet<>(); // 存放当前有个数的总和
		if (nums[0] > 0) {
			dp[0][nums[0]] = 1; // 正数有一个结果
			dp[0][nums[0] + 1000] = 1; // 负数有一个结果
			set.add(nums[0]);
			set.add(nums[0] + 1000);
		} else if (nums[0] == 0) {
			dp[0][nums[0]] = 2; // 正0和负0都是0
			set.add(nums[0]);
		} else {
			dp[0][-nums[0] + 1000] = 1;
			dp[0][-nums[0]] = 1;
			set.add(-nums[0]);
			set.add(-nums[0] + 1000);
		}
		for (int i = 1; i < n; i++) {
			Set<Integer> set2 = new HashSet<>();
			for (Integer index : set) { // 看看上一个dp哪些总和有数量
				if (index > 1000) { // 需要还原成真正的数字
					int temp = -(index - 1000); // 这是上一次真正的数字，是负数
					if (temp + nums[i] < 0) {
						dp[i][Math.abs(temp + nums[i]) + 1000] += dp[i - 1][index];
						dp[i][Math.abs(temp - nums[i]) + 1000] += dp[i - 1][index];
						set2.add(Math.abs(temp + nums[i]) + 1000);
						set2.add(Math.abs(temp - nums[i]) + 1000);
					} else {
						dp[i][temp + nums[i]] += dp[i - 1][index];
						set2.add(temp + nums[i]);
						if (temp - nums[i] >= 0) {
							dp[i][temp - nums[i]] += dp[i - 1][index];
							set2.add(temp - nums[i]);
						} else {
							dp[i][Math.abs(temp - nums[i]) + 1000] += dp[i - 1][index];
							set2.add(Math.abs(temp - nums[i]) + 1000);
						}
					}
				} else {
					dp[i][index + nums[i]] += dp[i - 1][index];
					set2.add(index + nums[i]);
					if (index - nums[i] >= 0) {
						dp[i][index - nums[i]] += dp[i - 1][index];
						set2.add(index - nums[i]);
					} else {
						dp[i][Math.abs(index - nums[i]) + 1000] += dp[i - 1][index];
						set2.add(Math.abs(index - nums[i]) + 1000);
					}
				}
			}
			set.clear();
			for (Integer integer : set2) {
				set.add(integer);
			}
		}
		if (S >= 0) {
			return dp[n - 1][S];
		} else {
			return dp[n - 1][Math.abs(S) + 1000];
		}
	}
```

# 五、双指针 #

## 11. 盛最多水的容器:white_check_mark: ##

> 给你`n`个非负整数`a1，a2，...，an`，每个数代表坐标中的一个点 `(i, ai) `。在坐标内画`n` 条垂直线，垂直线`i `的两个端点分别为 `(i, ai)` 和 `(i, 0)` 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202311151610714.jpeg" alt="img" style="zoom:80%;" />

```css
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

### 思路分析 ###

+ 显而易见，矩形的高遵循木桶原则，我们希望两个截面尽可能高的同时距离也尽可能远，这样面积最大
+ 我们如何表示两个截面呢？可以使用**双指针法**，让一头`i`指向开始，一头`j`指向结尾
+ `j-i`即表示长，`min(height[i],height[j])`表示高，两者乘积就是面积，将每次的面积都记录到`area`中
+ **我们希望横截面尽可能高，因此每次都让矮的横截面进行移动**，再次计算面积……直至`i==j`

### 代码实现 ###

```java
public int maxArea(int[] height) {
		int area = 0; // 面积
		for (int i = 0, j = height.length - 1; i != j;) {
			int x = j - i; // 长
			int y = Math.min(height[i], height[j]); // 宽
			if (x * y > area) {
				area = x * y;
			}
			if (height[i] < height[j]) {
				i++;
			} else {
				j--;
			}
		}
		return area;
	}
```

## 15. 三数之和:white_check_mark: ##

> 给你一个包含 `n `个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为` 0 `且不重复的三元组。
>
> 注意：答案中不可以包含重复的三元组。

```css
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

### 思路分析 ###

+ 首先面对杂乱无序的数组，我们很难下手，因此我们先对数组进行**排序**
+ 题目要求三数之和，显然**至少数组长度`n>=3`**，同时面对排好序的数组，**如果数组第一个元素就大于0，那么显然三数之和不可能等于0**；同理，**数组最后一个元素小于0也是不满足条件的**。因此我们利用`if`排除这三种情况
+ 我们可能会想到哈希表，利用哈希表将下标与值关联起来，再来一个双指针从头尾分别出发，将双指针经过的地方从哈希表中移除，遇到相同的数字只处理一次(防止序列重叠)，设置一个目标值`target=0-(nums[start]+nums[end])`，如果哈希表中存在这个数字，就说明有三数之和。这个方法听起来没有什么问题，但是在头尾指针移动过程中其实会出现一个bug……我们在处理指针时采取这样一种方式：当`-target(即头指针的值加尾指针的值)<0`时，说明左边占据主导地位，我们移动头指针；当`-target >0`时，说明右边占据主导地位，我们移动尾指针。但是`-target==0`怎么办呢？到底移动左指针还是右指针呢？假设移动头指针，`[-2,0,1,1,2]`就会漏下一个答案，反之，`[-2,-1,-1,0,2]`会漏下一个答案，由于我们对于序列是未知的，因此我们解决不了这个问题，所以此方法最终会败在这个点上，行不通
+ 但是指针的思路是正确的，我们可以对其进行升级变成**三指针**，其中`first`指向第一个数字(初始在0)，`second`指向第二个数字（初始在`first`后一位），`third`指向第三个数字(初始在末尾)。
+ 面对相同的数字我们同样只处理一次，所以当`first>0&&nums[first]==nums[first-1]`时就让头指针后移，锁定头指针后，目标值`target=-nums[first]`；接着处理`second`指针，同理，当`second>first+1&&nums[second]==nums[second-1]`时就让中间指针后移，另外中间指针下标一定是小于尾指针的
+ 当`target<nums[second]+nums[third]`，说明当前值太大，于是让尾指针前移；若`target==nums[second]+nums[third]`，就将指针对应的值加入链表中。
+ 通过上述过程可以看出，`first`是最先锁定的，是老大哥，所以当它发生变化时候，`second和third`都需要重置，`second`负责调大，`third`负责调小，属于同一纬度，因此是`first`嵌套着`second`和`third`

### 代码实现 ###

```java
public List<List<Integer>> threeSum(int[] nums) {
		List<List<Integer>> list = new ArrayList<List<Integer>>();
    	// 排序
		Arrays.sort(nums);	
		int n = nums.length;
		if(n<3||nums[0]>0||nums[n-1]<0) {
			return list;
		}
		for (int first = 0; first < n; first++) {
			if (first > 0 && nums[first] == nums[first - 1]) {
				// 有相同的数
				continue;
			}
			//nums[first]+nums[second]+nums[third]=0，
            //所以nums[second]+nums[third]=-nums[first]
			int target = -nums[first];
			int third = n - 1;
			for (int second = first + 1; second < third; second++) {
                // 有重复只记录一次
				if(second>first+1&&nums[second]==nums[second-1]) {
					continue;
				}
                // 比目标值大，负责调小
				while(second<third&&nums[second]+nums[third]>target) {
					third--;
				}
				if(second==third) {
					break;
				}
                // 等于目标值的序列加入
				if(nums[second]+nums[third]==target) {
					List<Integer> temp = new ArrayList<Integer>();
					temp.add(nums[first]);
					temp.add(nums[second]);
					temp.add(nums[third]);
					list.add(temp);			
				}
			}
		}
		return list;
	}
```

## 19. 删除链表的倒数第N个结点:white_check_mark: ##

> 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。
> **进阶：**你能尝试使用一趟扫描实现吗？

```css
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

### 思路分析 ###

+ 所谓**倒数第N个结点，实际上就是从最后一个结点往回数N-1次对应的节点**。但是链表不像数组可以直接得到长度，因此直接通过长度往回数的方式复杂度比较高，应该想想有没有其他方法
+ 我们发现，既然最后一个节点与要删除的节点距离是`N-1`，那如果我们先把这个距离固定下来，接着再进行移动，等走到链表末尾的时候，不就能确定要删除结点的位置了么？由此我们可以引出**双指针法**
+ 找到了要删除的结点`p1`后，要真正将其删除还需要一个前驱`p3`。同时我们还要考虑一种特殊情况，即要删除的结点就是头结点时，我们应该直接`return head.next`，改变开头的位置即删除了`head`。

### 代码实现 ###

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
		//倒数第n个节点与最后一个节点的距离是n-1
		ListNode p1=head; //要删除的索引
		ListNode p3=new ListNode(0);
		p3.next=head; //p3在head前一个
		ListNode p2=head; //后索引
		while(n>1&&p2!=null) {
			p2=p2.next;
			n--;
		}
		if(n!=1) {
			return  head;
		}
		while(p2.next!=null) {
			p1=p1.next;
			p2=p2.next;
			p3=p3.next;
		}
		if(p1==head) {
			return head.next;
		}
		p3.next=p3.next.next;
		return head;
	}
```

## 31. 下一个排列:white_check_mark: ##

> 实现获取 `下一个排列` 的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。<br>如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。<br>必须 `原地` 修改，只允许使用额外**常数**空间。

```css
输入：nums = [1,2,3]
输出：[1,3,2]
解释：即在123,132,321,312,231,213里面找到刚刚大过123的数字
```

### 思路分析 ###

+ 不难发现，数字位数越靠后影响到的东西越少，也就是说我们**尽量从后边找到可以交换的两个数字**
+ 交换部分的高位肯定是要变大的，否则无法保证比原来的数字大，但是我们希望它变大的幅度尽可能小
+ 我们可以从数组末尾开始找**第一次出现的两个紧挨的升序元素**，下标分别为`i`和`j`，**所有在这两个元素后边的肯定是降序【因为这两个数是从后往前数的第一次升序(从左往右看)，所以在遇到这两数之前肯定都是降序】**
+ 在`[j,结尾]`这一段是降序的，**最小的元素在结尾**，我们依次扫描，找到一个正好大过`nums[i]`的元素，将他们交换，此时我们就完成了将相对较大的一个数和高位的交换
+ 交换后`[j,结尾]`依旧是降序的，但是我们已经实现了高位升级，因此此处应该让`[j,结尾]`尽可能变小，才能使其最靠近题目给出的输入

### 代码实现 ###

```java
public void nextPermutation(int[] nums) {
		int temp;
		int n=nums.length;
		int end=-1; //升序结尾元素下标
		for(int i=n-1;i>0;i--) {
			//从后往前找到两个相邻升序元素
			if(nums[i]>nums[i-1]) {
				//找到升序位置就退出
				end=i;
				break;
			}
		}
		if(end==-1) {
			//说明整个数组都是降序，此时没有更大的值
			Arrays.sort(nums);
            return;
		}
		for(int i=n-1;i>=end;i--) {
			//剩下的部分是降序，末尾是最小的
			if(nums[i]>nums[end-1]) {
                //正好大过升序的第一个元素
				temp=nums[i];
				nums[i]=nums[end-1];
				nums[end-1]=temp;
				break;
			}
		}
		for(int i=n-1,j=end;i>j;i--,j++) {
            //使整个部分由降序变升序，使总值尽可能接近题目给出的值
			temp=nums[i];
			nums[i]=nums[j];
			nums[j]=temp;
		}
    }
```

## 75. 颜色分类 ##

> 给定一个包含红色、白色和蓝色，一共 n 个元素的数组，**原地**对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。
>
> 此题中，我们使用整数 `0`、 `1` 和 `2` 分别表示红色、白色和蓝色。

```css
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```

### 思路分析 ###

+ **双指针**，将等于0的放在`walk1`左边，将等于1的放在`walk1与walk2`之间，对整个数组进行遍历
+ 当指针指向正好是数字0或1时，对指针相应进行移动，提高效率
+ **当指针指向不是数字0或1了且扫描到的数字为0**，则说明当前数字应该放在`walk1`前面，因此进行交换，若此时1指针位置大于0指针，说明0指针指向的肯定是数字1，因此被交换到`nums[i]`的是数字1，而数字1是1指针需要的，因此还需要其与1指针交换。最后移动两个指针位置
+ 当指针指向不是数字0或1了且扫描到的数字为1，则说明当前数字应该放在`walk2`前面，因此进行交换且`walk2++`
+ 一次遍历后得到的即为正确答案

### 代码实现 ###

```java
public void sortColors(int[] nums) {
		if (nums.length == 1) {
			return;
		}
		int walk1 = 0; // 指向0的指针
		int walk2 = 0; // 指向1的指针
		int temp;
		for (int i = 0; i < nums.length; i++) {
			if (nums[walk1] == 0) {
				// 如果0指针指向就是0，则往后走一步
				walk1++;
				walk2++;
			} else if (nums[walk2] == 1) {
				// 如果1指针指向就是1，则1指针往后走一步
				walk2++;
			} else {
				if (nums[i] == 0) {
					// 当前扫描到的值为0时，说明这个值是属于0指针的，进行交换并且往后走一步
					temp = nums[i];
					nums[i] = nums[walk1];
					nums[walk1] = temp;
					if (walk1 < walk2) {
					// 如果1指针大于0指针，说明0指针当前指向是数字1
					// 因此nums[i]与0指针交换后一定是1，而1是1指针需要的，因此还要进行一次交换
						temp = nums[i];
						nums[i] = nums[walk2];
						nums[walk2] = temp;
					}
					walk1++;
					walk2++;
				} else if (nums[i] == 1) {
				// 当前扫描到的值为1时，说明这个值是属于1指针的，进行交换并且1指针往后走一步
					temp = nums[i];
					nums[i] = nums[walk2];
					nums[walk2] = temp;
					walk2++;
				}
			}
		}
	}
```

# 六、全排列 #

## 17. 电话号码的字母组合 ##

> 给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。<br>给出数字到字母的映射如下（与电话按键相同）。注意 `1` 不对应任何字母。

<img src="G:/%E5%A4%A7%E4%B8%89%E4%B8%8A/%E5%9B%BE%E7%89%87/image-20210124215300629.png" alt="image-20210124215300629" style="zoom: 67%;" align="left"/>

```css
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
说明：尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。
```

### 思路分析 ###

+ 题目的意思很明确，就是让我们列出**排列组合的所有可能**。我们可以用`map`保存按键和字母的映射关系，遍历字符串的每一个字符(键值`Key`)，根据相应按键得到不同结果，在每次的结果中取一个字符进行拼接，拼接的结果需要保存下来给下一个拼接……拼接完毕所有字符后，同样的流程又要再来一遍，这是重复做同一件事情，因此我们可以想到**递归**
+ 首先我们通过**构造函数**完成`Map<Character,String>`的映射，方便取值；接着创建`List<String>`用来接收每次拼接完毕的结果；由于我们需要将每次拼接的结果进行传递，同时还不影响到当前操作，因此我们可以将当前拼接结果放入参数中，又因为取映射值需要靠原字符串以及字符串索引，因此我们另写一个方法`Get(String digits,String s,int index)`用来实现拼接功能，其中**三个参数分别为原字符串、拼接结果、当前字符串索引**。初始调用时`s==""`且`index==0`，这是因为此时还没有开始拼接和遍历
+ 在`Get`方法里，首先要把原字符串当前索引对应的字符作为`键值Key`，在`map`中找到对应的`字符串value`，由于我们要覆盖所有的排列组合，所以`value`的每个值都会成为结果的一部分，但是不能同时出现，所以我们用一个`for`循环对这个`字符串value`进行遍历，每次取到一个值，并且将它进行拼接放入`str`中。
+ 此时字符串`value`的任务暂时结束了，需要进入原字符串的下一个索引进行处理，步骤是一样的，因此我们递归调用`Get(digits,str,index)`，其中`index`需要在`map`取出值后就进行`+1`更新，因为取出`map`的值后，`index`在本次`Get`的任务就结束了，需要为下一次`Get`做好准备。`str`为拼接好的字符串
+ 我们希望当遍历完一遍原字符串后递归就能结束，如何能做到呢？我们发现遍历原字符串的每一个字符时都会对应到一个按键值，因此当我们遍历完原字符串后，拼接的按键值长度应该和原字符串长度相等。也就是说**当拼接的按键值长度应该和原字符串长度相等时，我们就得到一个完整序列**，可以将其加入`List`中，并且递归到这里也会结束。

### 代码实现 ###

```java
public class LC017 {
	//用来存放按键
	Map<Character,String> map=new HashMap<Character,String>();
	List<String> list = new ArrayList<>();
	public LC017() {
		map.put('2', "abc");
		map.put('3', "def");
		map.put('4', "ghi");
		map.put('5', "jkl");
		map.put('6', "mno");
		map.put('7', "pqrs");
		map.put('8', "tuv");
		map.put('9', "wxyz");
	}
	public List<String> letterCombinations(String digits) {
		if(digits.length()==0) {
			return list;
		}
		Get(digits, "",0);
		return list;
    }
	
	public void Get(String digits,String s,int index) {
		if(s.length()==digits.length()) {
			//说明走到了最后一步
			list.add(s);
			return;
		}
		//获得当前字符对应的字母集
		String content = map.get(digits.charAt(index++));
		for(int i=0;i<content.length();i++) {
			String str=s+content.charAt(i);
			Get(digits, str,index);
		}
    }
}
```

## 22. 括号生成 ##

> 数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```css
输入：n = 3
输出：["((()))","(()())","(())()","()(())","()()()"]
```

### 思路分析 ###

+ 如果有`n`对括号，那么肯定有`n`个左括号和`n`个右括号；在整个放置过程中，左括号的数量必须恒大于等于右括号，否则会造成括号不匹配现象；当然最终左括号的数量也必须`<n`，否则不符合题目要求
+ 因此我们可以**优先在拼接字符串中尝试左括号**，看看有哪些组合方式；再将这个左括号变换成右括号，看看有哪些组合方式。这个过程是多次重复的，所以我们可以考虑**递归**
+ 当拼接的字符串长度为`2*n`，说明拼接完毕，可以结束递归；当左括号数目`<n`，说明左括号还可以继续放；当右括号数目`<左括号`，说明右括号还可以继续放。
+ 总体思想就是在**每个位置都尝试一下左右括号，用左括号约束右括号**

### 代码实现 ###

```java
public class LC022 {
	List<String> list = new ArrayList<String>();
	public List<String> generateParenthesis(int n) {
         Method(0, 0, new StringBuilder(), n);
        return list;
    }
	public void Method(int left,int right,StringBuilder result,int max) {
		if(result.length()==max*2) {	
			//说明字符串拼接完毕
			list.add(result.toString());
			return;
		}
		if(left<max) {
			//左括号还没达到最大，能放就放
			result.append("(");
			Method(left+1, right, result, max);
			result.deleteCharAt(result.length()-1);//减少一个左括号看看有没有其他情况
		}
		if(right<left) {
			//当前位置放不了左括号试一试右括号
			result.append(")");
			Method(left, right+1, result, max);
			result.deleteCharAt(result.length()-1);
		}
	}
}
```

## 39. 组合总数 ##

> 给定一个**无重复**元素的数组 `candidates` 和一个目标数` target `，找出 `candidates` 中所有可以使数字和为 `target `的组合。
>
> `candidates `中的数字可以无限制重复被选取。

```css
输入：candidates = [2,3,5], target = 8,
所求解集为：
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

### 思路分析 ###

+ 首先，当`target<数组元素`时，这元素肯定不会被选到，因此我们**先对整个数组进行排序**，排除掉一部分元素
+ 当题目要求涵盖各种可能性的时，我们很容易就能想到**回溯**：如果`target`最多能放`x`个当前元素`nums[index]`，将剩下的值`target-(nums[index]*x)`放到下一层递归中看看`nums[index+1]`最多又能放几个……如果`target>nums[index]`说明一个都放不下，那么直接`index--`进入下一个索引查看情况。
+ 将每一种情况的最多能放`x`个逐渐减至0，就将所有的可能性覆盖了。需要注意的是，由于链表是会划分空间的，因此在函数中改变了链表结构后，链表就被永远改变了，而我们希望递归返回上一层的时候能够保持参数原样。因此我们需要对递归后被改变的链表进行`remove`操作，使其恢复原样
+ 同样，当我们把`temp`加入到`list`中时不能直接添加`temp`，因为我们的`temp`结构一直发生变化，我们应该将当下的`temp`进行复制添加到`list`才不会被`temp`结构改变影响到。

### 代码实现 ###

```java
	List<List<Integer>> list=new ArrayList<List<Integer>>(); //存放最终结果
	public List<List<Integer>> combinationSum(int[] candidates, int target) {
		Arrays.sort(candidates);//从小到大排列
		int index=-1; //target可能的下标
		for(int i=candidates.length-1;i>=0;i--) {
			if(target>=candidates[i]) {
				index=i;
				break;
			}
		}
		if(index==-1) {
			return list;
		}
		targetSearch(candidates, target, new ArrayList<Integer>(), index);
		return list;
	}
	public void targetSearch(int[] candidates,int target,List<Integer> temp,int index) {
		if(target==0) {
			//说明此时组合已经完毕，可以将其添加进链表
			List<Integer> result = new ArrayList<>();
			for(int i=0;i<temp.size();i++) {
				result.add(temp.get(i));
			}
			list.add(result);
			return;
		}else if(index<0) {
			//索引小于0，说明没有找到合适的
			return;
		}
		if(target>=candidates[index]) {
			//目标值大于等于当前的值
			int number=target/candidates[index];  //目标数里可以包含几个当前扫描到的数
			while(number>=0) {
				int x=number; //当前数字可以添加几个
				int y=number; //递归后应该将当前添加的数字移除几个
				while(x>0) {
					temp.add(candidates[index]);
					x--;
				}
				targetSearch(candidates, target-(candidates[index]*number), temp, index-1);
				while(y>0) {
					temp.remove(temp.size()-1);
					y--;
				}
				//试一试下一种可能
				number--;
			}
		}
		else if(target<candidates[index]) {
			index--;
			targetSearch(candidates, target, temp, index);
		}
	}
```

## 46. 全排列 ##

> 给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

```css
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

### 思路分析 ###

+ 这是一道列出所有可能的问题，因此可以使用**回溯**
+ 首先，每个值都可能放在任意位置上，但是放过的地方不能再放数字，因此我们可以利用一个`boolean[] flag`来记录当前位置的存放情况，每次递归里都用一个`for`查看当前位置是否可放，如果可以就放入并进入下一个递归。当`temp长度==数组长度`时，说明所有位置都摆放完毕
+ 每次递归结束时，说明当前位置的所有情况都试过了，准备进入下一个位置试一试。需要将当前存放情况进行修改，避免递归的错误判断。

### 代码实现 ###

```java
List<List<Integer>> list=new ArrayList<List<Integer>>();
	public List<List<Integer>> permute(int[] nums) {
		int n=nums.length;
		boolean[] flag=new boolean[n];
		Search(nums, flag, new ArrayList<>());
		return list;
	}
	public void Search(int []nums,boolean []flag,List<Integer> temp) {
		if(temp.size()==nums.length) {
			List<Integer> result = new ArrayList<>();
			for ( Integer x : temp) {
				result.add(x);
			}
			list.add(result);
			return;
		}
		for(int i=0;i<nums.length;i++) {
			if(!flag[i]) {
				temp.add(nums[i]);
				flag[i]=true;
				Search(nums, flag, temp);
				flag[i]=false;
				temp.remove(temp.size()-1);
			}
		}
	}
```

## 78. 子集 ##

> 给你一个整数数组 `nums` ，数组中的元素 **互不相同** 。返回该数组所有可能的子集（幂集）。<br>解集 **不能** 包含重复的子集。你可以按 **任意顺序** 返回解集。

```css
输入：nums = [1,2,3]
输出：[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

### 思路分析 ###

+ 对于每个元素来说只有两种状态：**集合中有这个元素，集合中没有这个元素**
+ 我们对所有的元素状态进行排列组合，将所有的结果都添加到链表中即可。可以使用**递归**实现

### 代码实现 ###

```java
List<List<Integer>> list=new ArrayList<List<Integer>>();
	public List<List<Integer>> subsets(int[] nums) {
		Search(nums, 0, new ArrayList<Integer>());
		return list;
	}
	public void Search(int []nums,int index,List<Integer> temp) {
		if(index==nums.length) {
			List<Integer> mid = new ArrayList<>();
			for (Integer i : temp) {
				mid.add(i);
			}
			list.add(mid);
			return;
		}
		Search(nums, index+1, temp);
		temp.add(nums[index]);
		Search(nums, index+1, temp);
		temp.remove(temp.size()-1);
	}
```

# 七、链表 #

## 21. 合并两个有序链表 ##

> 将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

```css
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

### 思路分析 ###

+ 首先考虑特殊情况，如果两个链表有**至少一个为空**怎么办？自然是不需要进行排序，**直接返回不空的链表即可**
+ 此题在熟悉链表的基础上很容易就能想到。可以利用两个指针`p1`与`p2`分别对两个链表进行遍历，创建一头指针用于见机行事：两个指针对应的值哪个小就把哪个链接在头指针后边
+ 当一个链表走完了，另一个链表还没有走完怎么办？由于是有序链表，直接把剩下部分进行链接即可

### 代码实现 ###

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
		if(l1==null) {
			return l2;
		}else if(l2==null) {
			return l1;
		}
		ListNode head = new ListNode(0); //索引
		ListNode result= head; 
		ListNode p1 = l1;
		ListNode p2 = l2;
		while(p1!=null&&p2!=null) {
			if(p1.val<=p2.val) {
				head.next=p1;
				p1=p1.next;
				head=head.next;
			}else {
				head.next=p2;
				p2=p2.next;
				head=head.next;
			}
		}
		if(p1==null) {
			head.next=p2;
		}else if(p2==null) {
			head.next=p1;
		}
		return result.next;
	}
```

## 141. 环形链表 ##

> 给定一个链表，判断链表中是否有环。如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 <br>如果链表中存在环，则返回 `true` 。 否则，返回 `false` 。

### 思路分析 ###

+ 一个指针`p`速度快，一个指针`q`速度慢，起始位置`p`在`q`前面
+ 如果遇到环形，`p`指针会跑到`q`指针后面，如果速度相差是1的话，当`p`和`q`相等时就证明了环形的存在

### 代码实现 ###

```java
public boolean hasCycle(ListNode head) {
		if (head == null || head.next == null) {
			return false;
		}
		ListNode slow = head;
		ListNode fast = head.next;
		while (slow != fast) {
			if (fast == null || fast.next == null) {
				return false;
			}
			slow = slow.next;
			fast = fast.next.next;
		}
		return true;
	}
```

## 142. 环形链表II ##

> 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。<br>说明：不允许修改给定的链表。

### 思路分析 ###

+ 将每个扫描到的节点放入集合中，当扫描到的元素已经存在于集合中时，说明当下这个节点就是入环的第一个节点

### 代码实现 ###

```java
public ListNode detectCycle(ListNode head) {
		Set<ListNode> set = new HashSet<ListNode>();
		while(head!=null) {
			if(set.contains(head)) {
				return head;
			}
			set.add(head);
			head=head.next;
		}
		return null;
	}
```

## 160. 相交链表 ##

> 编写一个程序，找到两个单链表相交的起始节点。

```css
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

### 思路分析 ###

+ 链表A与B虽然长度不同，但是其具有公共部分，这是我们的突破口
+ **我们可以遍历链表A，走完后让其走B的路；遍历完链表B后，让其走A的路。当这两个节点相遇的时候其实都是走了A+B路程，由于后半段是一样的，因此相遇的点就是相交点**

### 代码实现 ###

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
		if(headA==null||headB==null) {
			return null;
		}
		ListNode p1=headA;
		ListNode p2=headB;
		boolean flag=true;
		boolean flag2=true;
		while(p1!=null || p2!=null) {
			if(p1==p2) {
				return p1;
			}
			if(p1==null&&flag) {
				p1=headB;
				flag=false;
				continue;
			}else if(p1==null&&!flag) {
				return null; //再次走到终点，说明没有相交点
			}
			if(p2==null&&flag2) {
				p2=headA;
				flag2=false;
				continue;
			}
			else if(p2==null&&!flag2) {
				return null;
			}
			p1=p1.next;
			p2=p2.next;
		}
		return null;
	}
```

## 206. 反转链表 ##

> 反转一个单链表

```css
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

### 思路分析 ###

+ 使用**递归**走到链表末尾，接着递归每次回调的时候就可以得到上一个节点，利用一个辅助节点`tmp`将其串联起来即可，同时记得改变指针指向
+ 多开辟一片空间，利用**头插法**也可以实现反转

### 代码实现 ###

```java
	ListNode tmp = new ListNode(0);
	public ListNode reverseList(ListNode head) {
		ListNode tmp2 = tmp;
		reverseList1(head);
		return tmp2.next;
	}
	public void reverseList1(ListNode head) {
		if (head == null) {
			return;
		}
        // 一下冲到末尾
		reverseList(head.next); 
        // 递归回调时，每次的head都是往上数一个，tmp是辅助节点，始终在head后边完成串联
		tmp.next = head; 
		head.next = null;
		tmp = tmp.next;
	}
```

## 234. 回文链表 ##

> 请判断一个链表是否为回文链表。

```css
输入: 1->2->2->1
输出: true
```

### 思路分析 ###

+ 首先对于回文的东西，我们可以发现其左右关于中心对称，因此可以借助**栈**来帮助解决问题，不过节点个数为偶数与奇数时，对中间节点的处理是不一样的，如何确定一个链表节点个数是偶数还是奇数时问题的关键。
+ 首先定位到中间位置可以使用快慢指针，若快指针最后为`null`，说明节点个数为奇数；反之为偶数。而栈可以跟着慢指针走并添加数据，直至快指针停下来。
+ 当节点为奇数时，说明栈内最后会添加一个中间的分割元素进去，因此将此元素去除后，慢指针继续往下走，逐个将元素与栈顶进行比较判断是否为回文；当节点个数为偶数时，说明栈内最后一个元素是属于左边部分的最后一个元素，因此不需要除去，慢指针直接往后走接着出栈比较是否回文即可
+ **如果不使用额外结构怎么解决呢？**
+ 同样需要用快慢指针锁定中心位置，判断链表长度奇偶性。接着对前半部分进行链表反转，同时往两个方向走进行回文判断即可

### 代码实现 ###

```java
public class LC234 {
	ListNode tmp;

	public boolean isPalindrome(ListNode head) {
		if (head == null || head.next == null) {
			return true;
		}
		ListNode p1 = head;
		ListNode p2 = head.next;
		boolean flag = false;
		while (p2 != null && p2.next != null) {
			p1 = p1.next;
			p2 = p2.next.next;
		}
		if (p2 != null) {
			flag = true;
		}
		p2 = p1.next;
		tmp = p1;
		reverseList1(head, p1);
		if (!flag) {
			p1 = p1.next;
		}
		while (p2 != null) {
			if (p1.val != p2.val) {
				return false;
			}
			p1 = p1.next;
			p2 = p2.next;
		}
		return true;
	}

	public void reverseList1(ListNode head, ListNode p1) {
		if (head == p1) {
			return;
		}
		reverseList1(head.next, p1); // 一下冲到末尾
		tmp.next = head; // 递归回调时，每次的head都是往上数一个，tmp是辅助节点，始终在head后边完成串联
		head.next = null;
		tmp = tmp.next;
	}

}
```

# 八、二分法 #

## 33. 搜索旋转排序数组 ##

> **升序**排列的整数数组 `nums `在预先未知的某个点上进行了旋转（例如，` [0,1,2,4,5,6,7] `经旋转后可能变为 `[4,5,6,7,0,1,2]` ）。<br>请你在数组中搜索 target ，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

```css
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

### 思路分析 ###

+ 有顺序的数组+查找某个元素，我们很容易就会联想到**二分查找**，这个方向是对的
+ 所谓旋转，其实就是把一部分数组整体移动到另一部分数组的前面或者后面去，我们可以发现，按照整体为单位的移动，其内部的序列是不会发生改变的，因此我们二分查找就以这两部分为单位分别进行。接下来只要找到两部分的临界点，问题就基本可以得到解决
+ 我们先处理数组长度为`1`的情况，此时直接与`target`进行比较即可
+ 临界点具有什么特点呢？
  1. 当开头第一个元素就是临界点时，它会比后一个元素大
  2. 当最后一个元素就是临界点时，它会比前一个元素小
  3. 当临界点位于数组中间部分时，它会比前后都要大
+ 我们找到临界点后，将目标值与临界点左右部分进行比较，看看去哪边进行二分查找，最后得到答案。

### 代码实现 ###

```java
public int search(int[] nums, int target) {
		int n = nums.length;
		if (n == 1) {
			if (nums[0] == target) {
				return 0;
			}
			return -1;
		}
		int temp = 0;
		for (int i = 0; i < n; i++) {
			if (i == 0) {
				if (nums[i] > nums[i + 1]) {
					temp = i;
					break;
				}
			} else if (i == n - 1) {
				if (nums[i] < nums[i - 1]) {
					temp = i - 1;
					break;
				}
			} else if (nums[i - 1] < nums[i] && nums[i] > nums[i + 1]) {
				temp = i;
				break;
			}
		}
		if (target <= nums[temp] && target >= nums[0]) {
			return halfSearch(nums, target, 0, temp); // 左边找结果
		} else if (target <= nums[n - 1] && target >= nums[temp + 1]) {
			return halfSearch(nums, target, temp + 1, n - 1); // 右边找结果
		} else {
			return -1;
		}
	}
	//二分查找
	public int halfSearch(int[] nums, int target, int left, int right) {
		int index = (left + right) / 2;
		int mid = nums[index]; // 中间值
		if (target == mid) {
			// 说明找到了
			return index;
		} else if (left >= right) {
			// 左边下标不能大过右边
			return -1;
		}
		if (target > mid) {
			// 说明mid不够大，应该往更大的右边找找
			return halfSearch(nums, target, index + 1, right);
		} else {
			return halfSearch(nums, target, left, index - 1);
		}
	}
```

## 34. 在排序数组中查找元素第一个和最后一个位置 ##

> 给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的开始位置和结束位置。<br>如果数组中不存在目标值` target`，返回` [-1, -1]`。
>
> **进阶：**你可以设计并实现时间复杂度为 O(log n) 的算法解决此问题吗？

```css
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

### 思路分析 ###

+ 这题的思路很容易：即先找到目标元素的下标，再往左右进行扩散寻找最大范围。重点在于使用何种查找算法，我们可以使用**二分查找**
+ 找到`index`后，在`for`里面利用一些逻辑关系进行扩散，特别需要注意边界情况

### 代码实现 ###

```java
public int[] searchRange(int[] nums, int target) {
		if (nums.length == 0) {
			return new int[] { -1, -1 };
		}
		int index = halfSearch(nums, target, 0, nums.length - 1);
		if (index == -1) {
			return new int[] { -1, -1 };
		} else {
			int start = index;
			int end = index;
			for (int i = index - 1, j = index + 1; i >= 0 || j < nums.length;) {
				if (i >= 0 && nums[i] == nums[index]) {
					start = i;
					i--;
				}
				if (j < nums.length && nums[j] == nums[index]) {
					end = j;
					j++;
				}
				if (((i >= 0 && nums[i] != nums[index]) || i < 0)
						&& (j >= nums.length || (j < nums.length && nums[j] != nums[index]))) {
					break;
				}
			}
			return new int[] { start, end };
		}

	}

	// 二分查找
	public int halfSearch(int[] nums, int target, int left, int right) {
		int index = (left + right) / 2;
		int mid = nums[index];
		if (target == mid) {
			return index;
		} else if (left >= right) {
			return -1;
		}
		if (target > mid) {
			return halfSearch(nums, target, index + 1, right);
		} else {
			return halfSearch(nums, target, left, index - 1);
		}
	}
```

## 240. 搜索二维矩阵II ##

> 编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：
>
> 每行的元素**从左到右升序**排列。
> 每列的元素**从上到下升序**排列。

```css
输入：matrix = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17,24],[18,21,23,26,30]], target = 5
输出：true
```

## 思路分析 ##

+ 由于矩阵是有规律的，因此我们利用**二分法**对每一行进行搜索即可解决问题
+ 另一个方法则是给一个当前数字变大或者变小两种方式。**如果比目标数字小，就让它往变大的方向走，如果比目标数字大，就让它往变小的方向走**
+ 不难发现，当元素处于矩阵右上角的时，往下就是变大，往左就是变小；当元素处于矩阵左下角时，往右就是变大，往上就是变小。我们利用这个特点设置初始位置，进行比对寻找即可

### 代码实现 ###

```java
	public boolean searchMatrix(int[][] matrix, int target) {
		for (int[] is : matrix) {
			if (target == is[0] || target == is[is.length - 1]) {
				return true;
			} else if (target > is[0] && target < is[is.length - 1]) {
				boolean flag = halfSearch(is, 0, is.length - 1, target);
				if (flag) {
					return true;
				}
			}
		}
		return false;
	}
	//二分
	public boolean halfSearch(int[] temp, int left, int right, int target) {
		if (left > right) {
			return false;
		}
		int midIndex = (left + right) / 2;
		int mid = temp[midIndex]; // 中间值
		if (target == mid) {
			return true;
		}
		if (target < mid) {
			return halfSearch(temp, left, midIndex - 1, target);
		}
		if (target > mid) {
			return halfSearch(temp, midIndex + 1, right, target);
		}
		return false;
	}
	//比对寻找
	public boolean searchMatrix2(int[][] matrix, int target) {
		int i = 0;
		int j = matrix[0].length - 1;
		while (i < matrix.length && j >= 0) {
			if (matrix[i][j] == target) {
				return true;
			}
			if (matrix[i][j] < target) {
				i++;
			} else if (matrix[i][j] > target) {
				j--;
			}
		}
		return false;
	}
```

# 九、找规律

## 20. 有效的括号 ##

> 给定一个只包括 `'('，')'，'{'，'}'，'['，']' `的字符串，判断字符串是否有效。
>
> 有效字符串需满足：
>
> 1. 左括号必须用相同类型的右括号闭合
> 2. 左括号必须以正确的顺序闭合。
> 3. 注意空字符串可被认为是有效字符串。

```css
输入: "{[]}"
输出: true
输入: "([)]"
输出: false
```

### 思路分析 ###

+ 看到**括号匹配**，我啪的一下，很快啊！马上想到**栈**。其特点是就近匹配，可包含覆盖
+ 匹配问题就像消消乐一样，当凑齐一左一右就可以消除，之后不再对其进行考虑。不过需要注意的是括号的顺序问题以及最后栈中存在括号代表什么
+ 搞清楚这些后就可以解决问题

### 代码实现 ###

```java
public boolean isValid(String s) {
		Stack<Character> stack = new Stack<Character>();
		for(int i=0;i<s.length();i++) {
			char ch=s.charAt(i);
			switch (ch) {
			case '(':
			case '{':
			case '[':
				stack.push(ch);
				break;
			case ')':
				if (stack.isEmpty()||!stack.pop().equals('(')) {
					return false;
				}
				break;
			case ']':
				if (stack.isEmpty()||!stack.pop().equals('[')) {
					return false;
				}
				break;
			case '}':
				if (stack.isEmpty()||!stack.pop().equals('{')) {
					return false;
				}
				break;
			default:
				break;
			}
		}
		if (stack.isEmpty()) {
			return true;
		} else {
			return false;
		}
	}
```

## 48. 旋转图像 ##

> 给定一个 n × n 的二维矩阵表示一个图像。<br>将图像顺时针旋转 90 度。
>
> 说明：<br>你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

```css
给定 matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

原地旋转输入矩阵，使其变为:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

### 思路分析 ###

+ 设`n`为数组长度，元素下标`[x,y]`经过顺时针90度旋转后下标会变为`[y,n-1-x]`
+ 而通过水平翻转，我们可以实现下标`[x,y]--->[n-1-x,y]`；通过主对角线翻转可以实现`[x,y]-->[y,x]`
+ 因此我们可以**先水平翻转再对角线翻转**，就可以实现`[x,y]--->[y,n-1-x]`的转换

### 代码实现 ###

```java
public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 水平翻转
        for (int i = 0; i < n / 2; ++i) {
            for (int j = 0; j < n; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - i - 1][j];
                matrix[n - i - 1][j] = temp;
            }
        }
        // 主对角线翻转
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
    }
```

## 49. 字母异位词分组 ##

> 给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

```css
输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]

所有输入均为小写字母。
不考虑答案输出的顺序。
```

### 思路分析 ###

+ 互为字母异位词的一个显而易见的规律是：**每个字母数量相同**。考虑到输入全为小写字母，不难发现可以通过将字符串转换为`char[]`进行排序，**互为字母异位词的排序序列是一样的**，这个就是我们的突破口
+ 我们可以利用`Map<String,List<String>`来映射存放，其中关键字是每个字符串排序后的序列，键值即字符串排序后这个序列的原字符串
+ 最终返回`map.values()`即所有的字母异位词

### 代码实现 ###

```java
public List<List<String>> groupAnagrams(String[] strs) {
		Map<String, List<String>> map = new HashMap<String, List<String>>();
		for (String string : strs) {
			char[] charArray = string.toCharArray();
			// 对字符串进行排序
			Arrays.sort(charArray);
			String key = new String(charArray);
			// 通过排序后序列，在哈希表中找相应列表
			List<String> list = map.getOrDefault(key, new ArrayList<>());
			// 将初始字符串加入其中
			list.add(string);
			map.put(key, list);
		}
		return new ArrayList<List<String>>(map.values());
	}
```

## 53. 最大子序和 ##

> 给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

```css
输入: [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

### 思路分析 ###

+ 如果遇到正数，毫无疑问需要算进去；如果遇到负数，则需要考虑当前`累加和pre+当前负数`，如果小于当前负数，说明先前的累加和不是最大的，需要重新开头计算。
+ 当然，`pre`的值每次都需要与`max`进行比较替换，因为`pre`一路走来肯定有最大值。

### 代码实现 ###

```java
public int maxSubArray(int[] nums) {
        int pre = 0, maxAns = nums[0];
        for (int x : nums) {
            pre = Math.max(pre + x, x);
            maxAns = Math.max(maxAns, pre);
        }
        return maxAns;
    }
```

## 56. 合并区间 ##

> 给出一个区间的集合，请合并所有重叠的区间。

```css
输入: intervals = [[1,4],[4,5]]
输出: [[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

### 思路分析 ###

+ 对于`nums[a,b]和nums[c,d]`来说，如果`c`处于`[a,b]`之间，那它们就可以合并。也就是说`c`越接近`a`就越有可能能进行合并，如果`a==c`那就一定能合并，如何让这种相近的数放在一起呢？答案是**排序**
+ 面对无序的数组，我们可以尝试排序后看看是否有帮助。如果我们以每一维数组的第一个元素为根据进行排序，可以发现，**凡是能合并的区间都会被放在一起**，这就是突破口
+ 由于结果返回的是二维数组，但是我们事先不知道二维数组大小，即是动态的。因此我们需要用链表`List<int[]>`模拟二维数组，最后将答案通过`toArray(new int[list.size()][])`方法转变回二维数组
+ 由于我们进行了排序，所以在某一维合并不了就会去到下一维，并且不会走回头路。也就是说我们总是对链表的最后一维进行判断：如果可以合并，就在这一维合并；如果不能合并，就创建新的链表元素，并且下次操作也是从此处开始判断

### 代码实现 ###

```java
public int[][] merge(int[][] intervals) {
		int n = intervals.length;
		if (n == 0) {
			return new int[0][2];
		}
		Arrays.sort(intervals, new Comparator<int[]>() {
			@Override
			public int compare(int[] o1, int[] o2) {
                //从小到大排序
				return o1[0] - o2[0];
			}
		});
		List<int[]> list = new ArrayList<int[]>();
		for (int i = 0; i < n; i++) {
			int L = intervals[i][0]; // 当前元素的左边值
			int R = intervals[i][1]; // 当前元素的右边值
			if (list.size() == 0 || list.get(list.size() - 1)[1] < L) {
				// 创建新分组
				list.add(new int[] { L, R });
			} else {
				// 在覆盖范围内选择右边最大的数字成为新的右边界
			list.get(list.size() - 1)[1] = Math.max(list.get(list.size() - 1)[1], R);
			}
		}
		return list.toArray(new int[list.size()][]);
	}
```

## 238. 除自身之外数组的乘积 ##

> 给你一个长度为 n 的整数数组` nums`，其中 n > 1，返回输出数组 `output` ，其中 `output[i]` 等于 `nums `中除` nums[i] `之外其余各元素的乘积。
>
> **提示：**题目数据保证数组之中任意元素的全部前缀元素和后缀（甚至是整个数组）的乘积都在 32 位整数范围内。<br>**说明:** 请**不要使用除法**，且在 O(n) 时间复杂度内完成此题。<br>**进阶：**你可以在常数空间复杂度内完成这个题目吗？（ 出于对空间复杂度分析的目的，输出数组不被视为额外空间。）

```css
输入: [1,2,3,4]
输出: [24,12,8,6]
```

### 思路分析 ###

+ 使用除法的思路很好想到，先将所有乘积得到，遍历后除法，对0元素进行特殊处理
+ 不使用除法的话，我们可以先创建一个结果数组`res`，从左至右遍历`nums[]`，`res[i]`存放第`i+1`个元素左边的乘积，`res[0]`左边为空，因此初始值为1；不难发现`res[i]=res[i-1]*nums[i]`。一次遍历结束后，`res`存放的就是除自身之外**左边元素的乘积**。
+ 为了节省空间，我们不另外使用空间存放右边元素的乘积，而是对`nums`进行改装，使之成为存放右边元素的乘积，**遍历方向从右往左**
+ 由于`nums[i]`在计算过程中是需要用到的，因此我们在每次改装之前都需要将`nums[i]`保存在一个临时变量中，接着再令`nums[i]=temp*nums[i+1]`，此时有`res[i]=res[i]*nums[i]`，即`左边元素乘积*右边元素乘积`，最后得到的`res`就是答案

### 代码实现 ###

```java
public int[] productExceptSelf(int[] nums) {
		int [] res=new int[nums.length];
		res[0]=1;
		for(int i=1;i<nums.length;i++) {
			res[i]=res[i-1]*nums[i-1];
		}
		int temp=nums[nums.length-1];
		int temp2;
		nums[nums.length-1]=1;	//初始化
		for(int i=nums.length-2;i>=0;i--) {
			temp2=nums[i];
			nums[i]=nums[i+1]*temp;
			res[i]=res[i]*nums[i];
			temp=temp2;
		}
		return res;
	}
```

## 283. 移动零 ##

> 给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。
>
> 1. 必须在原数组上操作，不能拷贝额外的数组。
> 2. 尽量减少操作次数。

```css
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

### 思路分析 ###

+ 设置一个索引`left`，用来指向当前元素应该放的位置，初始值为`-1`
+ 遍历数组，如果遇到`nums[i]!=0`，`left++`，说明这个元素现在应该放在`nums[left]`这个位置，如果`i!=left`，则将两者进行对调即可

### 代码实现 ###

```java
public void moveZeroes(int[] nums) {
        int left=-1;
        int temp;
        for(int i=0;i<nums.length;i++){
            if(nums[i]!=0){
                left++;
            if(i!=left){
                temp=nums[i];
                nums[i]=nums[left];
                nums[left]=temp;
            }
            }            
        }
    }
```

## 287. 寻找重复数 ##

> 给定一个包含` n + 1 `个整数的数组` nums` ，其数字都在 `1` 到 `n` 之间（包括 `1` 和 `n`），可知至少存在一个重复的整数。
>
> 假设 `nums` 只有 **一个重复的整数** ，找出 **这个重复的数** 。

```css
输入：nums = [3,1,3,4,2]
输出：3
```

### 思路分析 ###

+ 由于数字在`1`到`n`之间，因此我们可以每个数字的情况记录到对应下标去，即`nums[i]`记录`数字i`
+ 又因为我们要区分`nums[i]`中数字出现了一次还是两次，因此需要一种类似标志的东西进行区分。我们可以利用`int n=nums.length`，每当扫描到到一个元素时，我们就令其对应下标对应的值`+n`，当扫描到相同元素时，如果这个元素对应下标对应的值大于`n`，则说明这是第二次出现，因此返回扫描到的元素
+ 还有一个问题，如果我们令对应值+n了，那么**我们想取到+n之前的数字怎么操作呢？**答案是求余`%`，至此我们就可以解决问题了。

### 代码实现 ###

```java
public int findDuplicate(int[] nums) {
    int n=nums.length;
    for(int i=0;i<nums.length;i++){
        if(nums[nums[i]%n]>n){
            return nums[i]%n;
        }
        nums[nums[i]%n]=nums[nums[i]%n]+n;
    }
    return nums[0];
    }
```

## 394. 字符串解码 ##

> 给定一个经过编码的字符串，返回它解码后的字符串。<br>编码规则为: `k[encoded_string]`，表示其中方括号内部的 `encoded_string` 正好重复 `k` 次。注意 `k` 保证为正整数。<br>你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 `k` ，例如不会出现像 `3a` 或 `2[4]` 的输入。

```css
输入：s = "3[a]2[bc]"
输出："aaabcbc"
```

### 思路分析 ###

+ 我们遇到括号时，总是应该先处理括号里面的情况，将其倍数展开，然后再和前面的情况进行拼接。也就是说**我们希望能够先走到括号最内部，然后一步步往回退**，这和**栈**不谋而合
+ 我们定义两个栈：①倍数栈 ②存放上一步结果栈，其分界线是左括号。
+ 初始值`res=""`，因为上一步没有内容，自然为空；`mul=0`，因为此时没有扫描到数字，同时我们要处理倍数为多位数的情况`mul=mul*10+Integer.parseInt(c+"")`，因此设置为`0`是必要的
+ 当我们扫描到普通字符时，直接拼接到`res`即可；当我们扫描到数字时，则将字符转换成数字暂时存放到`mul`中，因为可能出现多位数
+ 当我们扫描到左括号时，说明一个新的起点要开始了，我们先把历史遗留解决：①将前边拼凑的结果暂时放入栈中 ②将倍数放入数字栈中，以便遇到右括号时候能够知道拼凑的字符需要复制多少遍；由于是新的起点，因此我们要将状态初始化处理，`mul=0,res=""`
+ 当我们扫描到右括号时，说明这个括号的内容结束了，我们需要做两件事：①把将拼凑的字符串赋值`X`倍，`X`正放在数字栈顶 ②将赋值完的字符串`temp`与上一步结果进行拼接，使之成为这个括号之外的真正结果，而上一步的结果正放在另一个栈顶。

### 代码实现 ###

```java
public String decodeString(String s) {
		Stack<Integer> multi_stack = new Stack<Integer>(); //存放倍数
		Stack<String> res_stack = new Stack<String>(); //存放结果
		int mul=0;
		String res="";
		for (Character c : s.toCharArray()) {
			if(c=='[') {
				multi_stack.push(mul);
				res_stack.push(res);
				res="";
				mul=0;
			}else if(c==']') {
				StringBuilder temp = new StringBuilder();
				Integer count = multi_stack.pop();
				while(count-->0) {
					temp.append(res);
				}
				res=res_stack.pop()+temp.toString();
			}else if(c>='0'&&c<='9') {
				mul=mul*10+Integer.parseInt(c+"");
			}else {
				res=res+c;
			}
		}
		return res;
	}
```

## 406. 根据身高重建队列 ##

> 假设有打乱顺序的一群人站成一个队列，数组 `people` 表示队列中一些人的属性（**不一定按顺序**）。每个 `people[i] = [hi, ki]` 表示第` i` 个人的身高为` hi` ，前面 正好 有 `ki `个身高大于或等于 `hi `的人。
>
> 请你重新构造并返回输入数组` people` 所表示的队列。返回的队列应该格式化为数组` queue` ，其中 `queue[j] = [hj, kj] `是队列中第 `j `个人的属性（`queue[0]` 是排在队列前面的人）。

```css
输入：people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
输出：[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
解释：
编号为 0 的人身高为 5 ，没有身高更高或者相同的人排在他前面。
编号为 1 的人身高为 7 ，没有身高更高或者相同的人排在他前面。
编号为 2 的人身高为 5 ，有 2 个身高更高或者相同的人排在他前面，即编号为 0 和 1 的人。
编号为 3 的人身高为 6 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
编号为 4 的人身高为 4 ，有 4 个身高更高或者相同的人排在他前面，即编号为 0、1、2、3 的人。
编号为 5 的人身高为 7 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
因此 [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]] 是重新构造后的队列。
```

### 思路分析 ###

+ 题目的意思就是把`people`中有几个人比当前的人高能够在答案数组上直观看出来
+ **矮的人插队并不会对高的人有影响**，比如`X`比`Y`高，`X`在`Y`前面，`X`说前面比自己高的有`1`人，那么即使`Y`跑到`X`前面，`X`也依然说前面比自己高的有`1`人。所以我们让高的排前面先，即**根据`people[i][0]`进行降序排序**
+ 我们还发现，虽然矮的插队不会对高的有所影响，但是同样高的人插队却会令双方都有所影响，因此**同样高的人里**，我们还需要根据其前面有几人比他高进行升序排序，即根据**`people[i][1]`进行升序排序**
+ 对`people`排序完毕后，我们创建一个链表对结果进行拼接，首先进来的是最高的，毫无疑问它站第一位……后面来的人肯定都会比队伍里的人矮**(从高到低排序了)**，因此每次进来的时候判断有几个人比他高，他就站在这几个人后面，由于他比当前队伍的人都要矮，所以即使他插队到前面，对于其他人来说也是没有关系的。

### 代码实现 ###

```java
public int[][] reconstructQueue(int[][] people) {
		List<int[]> list = new ArrayList<int[]>();
		Arrays.sort(people, new Comparator<int[]>() {
			@Override
			public int compare(int[] o1, int[] o2) {
				if (o1[0] == o2[0])
					return o1[1] - o2[1];
				return o2[0] - o1[0];
			}
		});
		for (int[] is : people) {
			if (is[1] == list.size()) {
				list.add(is);
			} else if (is[1] < list.size()) {
				list.add(is[1], is);
			}
		}
		return list.toArray(new int[][] {});
	}
```

# 十、二叉树 #

## 98. 验证二叉搜索树 ##

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。<br>假设一个二叉搜索树具有如下特征：
>
> + 节点的左子树只包含小于当前节点的数。
> + 节点的右子树只包含大于当前节点的数。
> + 所有左子树和右子树自身必须也是二叉搜索树。

```css
输入:
    5
   / \
  1   4
     / \
    3   6
输出: false
解释: 输入为: [5,1,4,null,null,3,6]。
     根节点的值为 5 ，但是其右子节点值为 4 。
```

### 思路分析 ###

+ **二叉搜索树中序遍历结果为从小到大排列**
+ 因此我们可以用一个`temp`保留上一次遍历的节点值，如果当前节点的值小于或者等于`temp`，那就说明这不是二叉搜索树；左边验证后若返回`true`则继续向右边验证，当两边验证结果都是`true`时说明这是一棵二叉搜索树

### 代码实现 ###

```java
	int temp = Integer.MIN_VALUE;
	boolean flag2=false; //处理第一个数为Integer.MIN_VALUE的情况
	public boolean isValidBST(TreeNode root) {
		if (root == null) {
			return true;
		}
		boolean flag = isValidBST(root.left);
		if (root.val <= temp && flag2) {
			return false;
		} else {
			flag2=true;
			temp = root.val;
		}
		return flag == true ? isValidBST(root.right) : false;
	}
```

## 101. 对称二叉树 ##

> 给定一个二叉树，检查它是否是镜像对称的。

```css
例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

### 思路分析 ###

+ 两棵树在什么情况下互为镜像？如果同时满足下面的条件，两棵树互为镜像：
  + 它们的**两棵根结点具有相同的值**
  + **每棵树的右子树都与另一棵树的左子树镜像对称**
+ 我们可以用两个指针`p`和`q`分别指向一棵树的两边，当`p.left和q.right`对称且`p.right和q.left`对称且`p.val==q.val`时，就是对称二叉树
+ 当`p与q都==null`时也是对称的，这是退出条件，返回`true`；当`p和q只有一个为null`，说明不对称，返回false

### 代码实现 ###

```java
public boolean isSymmetric(TreeNode root) {
		return check(root, root);
	}
public boolean check(TreeNode p, TreeNode q) {
    if (p == null && q == null) {
        return true;
    }
    if (p == null || q == null) {
        return false;
    }
    return p.val == q.val && check(p.left, q.right) && check(p.right, q.left);
}
```

## 102. 二叉树的层序遍历 ##

> 给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）。

```css
二叉树：[3,9,20,null,null,15,7],
	3
   / \
  9  20
    /  \
   15   7
输出：
[
  [3],
  [9,20],
  [15,7]
]
```

### 思路分析 ###

+ 通常广度优先遍历我们都会想到**队列**，具体用法是用队列保存下一层的节点，并且在当前层需要把旧元素全部清空。
+ 我们首先将根节点加入队列，当队列不为空，说明下一层仍然有节点，因此需要进入循环
+ **得到当前队列的长度**，此长度即当前层元素的个数，进入`for`循环，将当前层元素的值全部加到`list`中。
+ 每次得到一个当前层元素就要将其从队列中移除，保证进入下一次循环时候没有旧元素；且对当前层元素的左右进行判断，如果不为空，则将其加入到队列中，表示这是下一层的元素
+ `for`循环结束后说明当前层的元素的值已经全部加入到`list`中，并且已经将当前层的下一层元素全部放入到队列中，此时将`list`加入到结果集中，当队列不为空时，再次进入循环

### 代码实现 ###

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> ret = new ArrayList<List<Integer>>();
    if (root == null) {
        return ret;
    }
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while(!queue.isEmpty()) {
        List<Integer> list = new ArrayList<>();
        int size = queue.size();
        for(int i=1;i<=size;i++) {
            TreeNode cur = queue.poll();
            list.add(cur.val);
            if(cur.left!=null) {
                queue.offer(cur.left);
            }
            if(cur.right!=null) {
                queue.offer(cur.right);
            }
        }
        ret.add(list);
    }
    return ret;
}
```

## 104. 二叉树的最大深度 ##

> 给定一个二叉树，找出其最大深度。<br>二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。<br>**说明:** 叶子节点是指没有子节点的节点。

```css
给定二叉树 [3,9,20,null,null,15,7]
    3
   / \
  9  20
    /  \
   15   7
最大深度为：3
```

### 思路分析 ###

+ 左子树与右子树的最大深度+1即树的最大高度
+ 我们传入初始长度1，接着对左右子树进行**递归**得到最大的高度
+ 在递归中，如果节点==null，说明递归之路到此为止，直接返回记录数字`count`；若当前节点!=null，则`count++`，将当前节点的数目记上，接着左右进行递归比较，返回较大的值

### 代码实现 ###

```java
public int maxDepth(TreeNode root) {
		if(root==null) {
			return 0;
		}
		return Math.max(searchMaxDepth(root.left, 1), searchMaxDepth(root.right, 1));
	}
public int searchMaxDepth(TreeNode root,int count) {
    if(root==null) {
        return count;
    }
    count++;
    return Math.max(searchMaxDepth(root.left, count), searchMaxDepth(root.right, count));
}
```

## 105. 从前序与中序遍历序列构建二叉树 ##

> 根据一棵树的前序遍历与中序遍历构造二叉树。<br>**注意:**你可以假设树中没有重复的元素。

```css
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
    3
   / \
  9  20
    /  \
   15   7
```

### 思路分析 ###

+ 前序与中序都由三部分【根、左、右】组成，不管哪种遍历方式，他们子树的大小都是一样的
  + 前序：根节点、【左子树】、【右子树】
  + 中序：【左子树】、根节点、【右子树】
+ **前序遍历的第一个一定是根节点**，因此我们可以先将其定下来；随后我们要寻找左子树和右子树的分界线，如何寻找呢？中序可以帮助我们
+ 由于前序已经知道了根节点，因此中序只需要遍历数组，将根节点的值与数组元素逐一对比，如果相同的话，就可以定下分界点。其左边的元素都是属于左子树的
+ 我们通过这个左子树的大小，就可以确定左子树在前序中的分界点，分别对数组中左子树与右子树的部分进行**递归**，当前节点的左边就是左子树，当前节点的右边就是右子树
+ 当我们发现数组大小传进来的子树只有一个元素时【即左右边界相等】，我们直接返回这个元素对应节点；当我们发现左边索引大于右边索引，说明出现越界，即不存在这种节点，返回`null`

### 代码实现 ###

```java
	public TreeNode buildTree(int[] preorder, int[] inorder) {
		return build(0, preorder.length - 1, preorder, inorder, 0, inorder.length - 1);
	}

	public TreeNode build(int leftIndex, int rightIndex, int[] preorder, int[] inorder, int left, int right) {
		if (leftIndex == rightIndex || left == right) {
			return new TreeNode(preorder[leftIndex]);
		} else if (leftIndex > rightIndex || left > right) {
			return null;
		}
		TreeNode node = new TreeNode(preorder[leftIndex]); // 前序第一个一定是根节点
		int count = 0;
		for (int i = left; i <= right; i++) {
			// 找到相隔多少个
			if (inorder[i] == node.val) {
				break;
			} else {
				count++;
			}
		}
		node.left = build(leftIndex + 1, leftIndex + count, preorder, inorder, left, left + count - 1);
		node.right = build(leftIndex + count + 1, rightIndex, preorder, inorder, left + count + 1, right);
		return node;
	}
```

## 226. 翻转二叉树 ##

> 翻转一棵二叉树。

```css
输入				
     4						
   /   \
  2     7
 / \   / \
1   3 6   9
输出
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

### 思路分析 ###

+ 所谓翻转二叉树，不难发现，实际上就是**每棵子树的左右位置互换**，知道这点后问题可以很好得到解决

### 代码实现 ###

```java
public TreeNode invertTree(TreeNode root) {
		reverse(root);
		return root;
	}

	public void reverse(TreeNode root) {
		if (root == null) {
			return;
		}
		TreeNode temp = root.left;
		root.left = root.right;
		root.right = temp;
		reverse(root.left);
		reverse(root.right);
	}
```

## 236. 二叉树的最近公共祖先 ##

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。
>
> `百度百科`中最近公共祖先的定义为：“对于有根树 `T` 的两个节点 `p`、`q`，最近公共祖先表示为一个节点 `x`，满足 `x` 是 `p`、`q` 的祖先且 `x` 的深度尽可能大**（一个节点也可以是它自己的祖先）**。”

```css
输入：root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出：3
解释：节点 5 和节点 1 的最近公共祖先是节点 3 。
```

### 思路分析 ###

+ 首先当然要确定`p、q`节点在二叉树中的位置，**当前节点以及当前节点之上的节点都可以成为祖先节点**。我们的目标是找到一个节点`ans`，它既可以成为`p`的祖先，也可以成为`q`的祖先，那么这就是我们的答案
+ 我们可以采用**后序**的方式，如果当前节点为`null`，那么显然这个节点不可能是目标`p或q`，因此返回`false`，当这个节点是`p或q`时，由于自身可以成为自身的祖先，因此返回`true`
+ 如果当前节点的左右有一个为`true`，说明在这个节点下面是有目标节点的，因此这个节点也可以成为祖先，返回`true`；
+ 当我们发现这个节点的左右都是`true`的时，说明左右都有目标节点，即当前节点是祖先节点`ans`；
+ 当我们发现左右虽然只有一个目标节点，但是当前节点也是目标节点之一时，当前节点也是祖先节点

### 代码实现 ###

```java
	TreeNode ans = null;
	public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
		DFS(root, p, q);
		return ans;
	}
	public boolean DFS(TreeNode root, TreeNode p, TreeNode q) {
		if (root == null || ans != null) {
			return false;
		}
        // 左子树情况
		boolean left = DFS(root.left, p, q); 
        // 右子树情况
		boolean right = DFS(root.right, p, q);
		if (left && right || (root.val == p.val || root.val == q.val) && (left || right)) {
		// 当节点左右都为true或者左右有一个为true且当前节点是p或者q其中一个时，当前节点是祖先节点
			ans = root;
		}
		// 节点左右有一个true就返回true，节点值是目标节点之一也
		return left || right || (root.val == p.val || root.val == q.val);
	}
```

## 538. 把二叉搜索树转换为累加树 ##

> 给出二叉 搜索 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 `node` 的新值等于原树中**大于或等于** `node.val` 的值之和。
>
> 提醒一下，二叉搜索树满足下列约束条件：
>
> + 节点的左子树仅包含键 **小于** 节点键的节点。
> + 节点的右子树仅包含键 **大于** 节点键的节点。
> + 左右子树也必须是二叉搜索树。

```css
输入：[4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
输出：[30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

### 思路分析 ###

+ 二叉搜索树，**右边值>中间值>左边值**。所以**在整棵树的最右边是最大的**，此时它的和就是它本身，接着是它中间那颗树，它的值是右边的值+自己的值；再接下来是左边的树，它的值是中间的值+自己的值……
+ 因此我们可以使用**右-->中-->左**的方式对整棵树进行扫描，被扫描到的节点值会变成**上一步传下来的节点值+自己的节点值**，当节点为`null`时，说明当前不能再往下了，所以返回传进来的值`count`

### 代码实现 ###

```java
public TreeNode convertBST(TreeNode root) {
		TreeNode res=root;
		reMid(root, 0);
		return res;
	}

	public int reMid(TreeNode root, int count) {
		if (root == null) {
			return count;
		}
		int rCount = reMid(root.right, count);
		root.val = root.val + rCount;
		return reMid(root.left, root.val);
	}
```

## 543. 二叉树的直径 ##

> 给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条**路径可能穿过也可能不穿过根结点**。

```css
          1
         / \
        2   3
       / \     
      4   5    
返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。
```

### 思路分析 ###

+ 对于一个节点来说，它的**最大深度**一定是`MAX(左边的最大深度,右边的最大深度)+1`
+ 一个节点高度为其左边或者右边的高度`+1`

### 代码实现 ###

```java
int max = 0;
public int diameterOfBinaryTree(TreeNode root) {
    DFS(root);
    return max;
}
public int DFS(TreeNode root) {
    if (root == null) {
        return -1;
    }
    int leftDepth = DFS(root.left) + 1;
    int rightDepth = DFS(root.right) + 1;
    max = Math.max(max, leftDepth + rightDepth);
    return Math.max(leftDepth, rightDepth);
}
```

## 617. 合并二叉树 ##

> 给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。
>
> 你需要将他们合并为一个**新的二叉树**。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则**不为** `NULL` 的节点将直接作为新二叉树的节点。

### 思路分析 ###

+ 我们应该同时同方向遍历两棵树，如果发现有一棵树是空的，那么当然就**把剩下不空的树整个复制到新二叉树**
+ 当我们发现两棵树都不空的时，我们创建一个新节点，节点值为两个节点的`val`之和，新节点的左节点为两棵树向左递归的结果；右节点为两棵树向右递归的结果
+ 最后返回的这个节点就是答案

### 代码实现 ###

```java
public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
        if (t1 == null) {
            return t2;
        }
        if (t2 == null) {
            return t1;
        }
        TreeNode merged = new TreeNode(t1.val + t2.val);
        merged.left = mergeTrees(t1.left, t2.left);
        merged.right = mergeTrees(t1.right, t2.right);
        return merged;
    }
```

# 十一、贪心算法 #

## 55. 跳跃游戏 ##

> 给定一个**非负整数数组**，你最初位于数组的第一个位置。<br>数组中的每个元素代表你在该位置可以跳跃的最大长度。<br>判断你是否能够到达最后一个位置。

```css
输入: [2,3,1,1,4]
输出: true
解释: 我们可以先跳 1 步，从位置 0 到达位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。

输入: [3,2,1,0,4]
输出: false
解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。
```

### 思路分析 ###

+ 题目要求只要有一种方式可以达到终点就返回`true`，我们可以利用**贪心算法**
+ 首先每个元素都有其能够触碰到的最远距离，在这个**最远距离之前所有的元素都可以被访问到**，访问新的元素后又可能产生新的最远距离……我们不关心它是怎么"跳"过去的，我们只需要知道在最远距离之前的每一个元素都会被访问到就行了
+ 当被访问到的元素值`nums[i]`与其下标`i`的值等于数组末尾索引值，就说明有这么一条路径，可以返回`true`

### 代码实现 ###

```java
public boolean canJump(int[] nums) {
        int n = nums.length;
        int rightmost = 0; //最远距离
        for (int i = 0; i < n; ++i) { //把每种可能都试一遍
            if (i <= rightmost) { //下标小于等于最远距离
                rightmost = Math.max(rightmost, i + nums[i]);
                if (rightmost >= n - 1) { //说明到了最后
                    return true;
                }
            }else {
            	break;
            }
        }
        return false;
    }
```

## 79. 单词搜索 ##

> 给定一个二维网格和一个单词，找出该单词是否存在于网格中。
>
> 单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

```css
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
给定 word = "ABCCED", 返回 true
给定 word = "SEE", 返回 true
给定 word = "ABCB", 返回 false
```

### 思路分析 ###

+ 走过的路不能再走，因此我们可以用个同等大小的`boolean[][]`来标记走的情况
+ 遍历二维数组的每一个点，如果这一点是可行的，则将其标记为`true`并对剩下四个方向进行寻找，如果这点不可行，直接寻找下一个点
+ 一个点四个方向都尝试过后要返回上一层进行尝试，并且需要将此点标记为`false`，使其不影响到其他路径的寻找
+ 最后如果能将字符串遍历完，说明有这么一条路径，可以返回`true`；如果尝试了二维数组的每一个点还是无法将字符串遍历完，说明不存在这条路径，返回`false`

### 代码实现 ###

```java
public boolean exist(char[][] board, String word) {
		int h = board.length, w = board[0].length;
		boolean[][] visited = new boolean[h][w];
		for (int i = 0; i < h; i++) {
			for (int j = 0; j < w; j++) {
				boolean flag = check(board, visited, i, j, word, 0);
				if (flag) {
					return true;
				}
			}
		}
		return false;
	}

	public boolean check(char[][] board, boolean[][] visited, int i, int j, String s, int k) {
		if (board[i][j] != s.charAt(k)) {
			return false;
		} else if (k == s.length() - 1) {
			return true;
		}
		visited[i][j] = true;
		int[][] directions = { { 0, 1 }, { 0, -1 }, { 1, 0 }, { -1, 0 } };
		boolean result = false;
		for (int[] dir : directions) {
			int newi = i + dir[0], newj = j + dir[1];
			if (newi >= 0 && newi < board.length && newj >= 0 && newj < board[0].length) {
				if (!visited[newi][newj]) {
					boolean flag = check(board, visited, newi, newj, s, k + 1);
					if (flag) {
						result = true;
						break;
					}
				}
			}
		}
		visited[i][j] = false;
		return result;
	}
```

## 121. 买股票的最佳时机 ##

> 给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i`天的价格。<br>你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。<br>返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

```css
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

### 思路分析 ###

+ 记录下买当前股票时的历史最低价格，**买股票的钱减去历史最低价格即为赚的钱**
+ 如果遍历的当前股票价格小于历史最低价格，则没有相减的必要且需要更新历史最低价格

### 代码实现 ###

```java
public int maxProfit(int[] prices) {
		if(prices.length==1) {
			return 0;
		}
		int min = prices[0]; // 历史最低点
		int max = 0; // 最大利润
		for (int i = 1; i < prices.length; i++) {
			if (prices[i] < min) {
				min = prices[i];
			} else if (prices[i] > min) {
				max = max >= (prices[i] - min) ? max : (prices[i] - min);
			}
		}
		return max;
	}
```

# 十二、位运算 #

## 136. 只出现一次的数字 ##

> 给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。<br>说明：你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

```css
输入: [4,1,2,1,2]
输出: 4
```

### 思路分析 ###

+ 很容易能想到的一个方法就是对数组进行排序再进行遍历，但是在这里我们可以尝试**异或运算**
+ ①**任何数和`0`做异或运算，结果仍然是原来的数** ②**任何数和其自身做异或运算，结果是 0**
  ③**异或运算满足交换律和结合律**
+ 因此我们可以对整个数组进行异或运算，有两个数字的异或后都会等于0，最最后剩下一个数字和0异或，答案就是这个数字

### 代码实现 ###

```java
public int singleNumber(int[] nums) {
        int single = 0;
        for (int num : nums) {
            single ^= num;
        }
        return single;
    }
```

## 338. 比特位计数 ##

> 给定一个非负整数 **num**。对于 **0 ≤ i ≤ num** 范围中的每个数字 **i** ，计算其二进制数中的 1 的数目并将它们作为数组返回。

```css
输入: 5
输出: [0,1,1,2,1,2]
```

### 思路分析 ###

+ 遇到比特相关问题，需要统计`1`的个数，都可以考虑**布赖恩·克尼根算法**`temp&(temp-1);`

### 代码实现 ###

```java
public int[] countBits(int num) {
        int temp=0;
        int temp2=0;
        int [] res=new int[num+1];
        while(temp<=num){
            int count=0;
            while(temp>0){
                count++;
                temp=temp&(temp-1);
            }
            res[temp2++]=count;
            temp=temp2;
        }
        return res;
    }
```

## 461. 汉明距离 ##

> 两个整数之间的汉明距离指的是这**两个数字对应二进制位不同的位置的数目**。
>
> 给出两个整数 `x` 和 `y`，计算它们之间的汉明距离。

```css
输入: x = 1, y = 4
输出: 2
解释:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
上面的箭头指出了对应二进制位不同的位置。
```

### 思路分析 ###

+ 首先需要了解异或运算符`^`和与运算符`&`，其他运算符具体如下[运算符详答](https://blog.csdn.net/mofeigege/article/details/106304076?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161447949316780265434845%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=161447949316780265434845&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-106304076.pc_search_result_before_js&utm_term=%E4%BD%8D%E8%BF%90%E7%AE%97%E7%AC%A6)
+ 十进制数字做位运算本质上会在二进制情况下按位操作，最后再以十进制展示结果。因此我们汉明距离只需要对这两个数字进行异或运算，**新数字的二进制位中1的个数就是汉明距离**
+ 我们还发现，一个数字-1后，变化的部分其实是最小的二进制位取反，如`101000`减1后变成`100111`，我们利用这个特点，可以统计出结果中`1`的个数
+ 如果将-1后的结果与原数字进行`与&`运算，会发现当前最小二进制位会变成0，即**整体1的个数少了1个**。我们利用这个方法进行统计，当原数字变成0时，说明所有的1都被消灭了，返回统计的汉明距离即可

### 代码实现 ###

```java
public int coinChange(int[] coins, int amount) {
		int[] dp = new int[amount + 1]; // 存放当前价格硬币最少要几个
		Arrays.fill(dp, amount + 1); // 硬币最小为1，因此需要的硬币不可能比要求的数量还大，所以amount+1是最大值
		dp[0] = 0; // 价格元为只需要0枚硬币
		for (int i = 1; i <= amount; i++) {
			for (int j = 0; j < coins.length; j++) {
				if (coins[j] <= i) { // 当币值小于总价格时候
					dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1); // 距离一步到达的地方最少硬币数
				}
			}
		}
		return dp[amount] > amount ? -1 : dp[amount];
	}
```

# 十三、排序 #

## 148. 排序链表 ##

> 给你链表的头结点 `head` ，请将其按 **升序** 排列并返回 排序后的链表 。<br>**进阶：**你可以在 `O(n log n)` 时间复杂度和常数级空间复杂度下，对链表进行排序吗？

```css
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
```

### 思路分析 ###

+ **归并排序**：将一整串先分成许多小部分，对小部分进行排序后再向上扩充，对稍大一点的部分进行排序，由于排序的两部分各自有序，因此排序的时间会少很多。
+ 首先我们需要找到链表中间部分才可以进行分割操作，其中**快慢指针**时一个不错的方法：一个指针速度为`2`，一个速度为`1`，当`2`的走到链表尾部时，`1`的就会正好走到链表中间
+ 我们利用`left`充当左边排序好的链表头节点；`right`充当右边排序好的链表头节点
+ 我们还需要一个辅助节点帮忙将`left与right`串联起来，当`left<right`，它就往左边跑，反之往右边跑。由于`left和right`本身是有序的，因此当一个`left或者right`有一个为空时，直接让辅助节点衔接不空的节点即可

### 代码实现 ###

```java
public ListNode sortList(ListNode head) {
		if (head == null || head.next == null) {
			return head;
		}
		ListNode slow = head;
		ListNode fast = head.next;
		while (fast != null && fast.next != null) {
			fast = fast.next.next;
			slow = slow.next;
		}
		ListNode tmp = slow.next;
		slow.next = null;
		ListNode left = sortList(head);
		ListNode right = sortList(tmp);
		ListNode h = new ListNode(0); //结果集头节点
		ListNode res = h; //结果集指针
		while (left != null && right != null) {
			if (left.val > right.val) {
				res.next = right;
				right = right.next;
			} else {
				res.next = left;
				left = left.next;
			}
			res = res.next;
		}
		res.next = left == null ? right : left;
		return h.next;
	}
```

## 169. 多数元素 ##

> 给定一个大小为 `n` 的数组，找到其中的多数元素。多数元素是指在数组中出现次数**大于**`⌊ n/2 ⌋`的元素。<br>你可以假设数组是非空的，并且给定的数组总是存在多数元素。

```css
输入：[3,2,3]
输出：3
```

### 思路分析 ###

+ 因为出现次数大于一半，因此我们通过**排序**后，这个元素一定会处于中间位置

### 代码实现 ###

```java
public int majorityElement(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length / 2];
    }
```

## 581. 最短无序连续子数组 ##

> 给你一个整数数组 `nums` ，你需要找出一个 **连续子数组** ，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。
>
> 请你找出符合题意的 **最短** 子数组，并输出它的长度。

```css
输入：nums = [2,6,4,8,10,9,15]
输出：5
解释：你只需要对 [6, 4, 8, 10, 9] 进行升序排序，那么整个表都会变为升序排序。。
```

### 思路分析 ###

+ 我们可以对原数组进行升序**排序**，再与原数组进行比对，可以知道：如果对应部分是相等的，说明在排序时其位置没有变动，也就是说它的位置原本就正确，成为升序数组与这部分无关
+ 如果发现对应位置不相等了，说明排序的调转位置是从这个地方开始的，我们记录下位置`left`；逆向遍历右边`right`同理，最后`right-left+1`就是答案长度。当然，如果整个数组本身就是递增数组，即对`left`和`right`不进行位置记录，其值为初始值就返回0
+ 另一种做法是利用**选择排序的原理**：利用栈记录升序下标，当遇到降序元素时，将栈顶下标对应的元素`nums[stack.peek()]`与当前元素进行比较，如果`nums[stack.peek()]`比较大，说明当前元素应该被换到前一个位置……直到找到适合的位置，并将这个位置入栈，`left`取值为`left=Math.min(left, stack.pop());`
+ 右边则是逆向遍历，遇到升序元素时则向后选位置，`right`取最大值

### 代码实现 ###

```java
public int findUnsortedSubarray(int[] nums) {
		Stack<Integer> stack = new Stack<Integer>();
		int left = nums.length;
		int right = 0;
		for (int i = 0; i < nums.length; i++) {
			while (!stack.isEmpty() && nums[i] < nums[stack.peek()]) {
				left=Math.min(left, stack.pop());
			}
			stack.push(i);
		}
		stack.clear();
		for (int i = nums.length - 1; i >= 0; i--) {
			while (!stack.isEmpty() && nums[i] > nums[stack.peek()]) {
				right=Math.min(right, stack.pop());
			}
			stack.push(i);
		}
		return right > left ? right - left + 1 : 0;
	}
```

# 十四、模拟 #

## 146. LRU缓存机制 ##

> 运用你所掌握的数据结构，设计和实现一个 `LRU` (最近最少使用) 缓存机制【操作系统的进程调度算法】

### 思路分析 ###

+ `Java`中为我们提供了`LinkedHashMap`可以实现`LRU`的基于双向链表的有序`Map`

  + 通常的`hashMap`只能实现键值映射关系，各个映射之间是无序的；而`LinkedHashMap`可以按照一定逻辑将键值映射进行排序，每个键值映射存放在一个链表节点中

  + ```java
    //构造函数对应参数分别是：初始容量，负载因子(0.75F)，
    //按照何种顺序排列(true为访问顺序，false为插入顺序，默认为false)
    //当其为插入顺序时，链表中的元素在被访问时不会发生位置交换；当其为访问顺序时，链表中被访问的元素会移动至链表末尾，使链表头始终是最久未使用的元素
    public LinkedHashMap(int initialCapacity,float loadFactor,boolean accessOrder)
    ```

  + ```java
    //当返回值为true时删除链表首元素，此方法会在put()时调用
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest)
    ```

+ 利用`LinkedHashMap`，我们可以实现`LRU`缓存机制，其中缓冲区大小即初始容量

### 代码实现 ###

```java
public class LC146 extends LinkedHashMap<Integer, Integer> {
	private int capacity; // 容量

	public LC146(int capacity) {
		super(capacity, 0.75F, true); 
		this.capacity = capacity;
	}

	public int get(int key) {
		return super.getOrDefault(key, -1);
	}

	// 这个可不写
	public void put(int key, int value) {
		super.put(key, value); // 将元素追加在队尾(队尾是最近被使用的元素)
	}

	@Override
	protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
		// removeEldestEntry是一个内部方法，会将链表队首元素删除
		return size() > capacity; // 当地图大小大于容量，说明需要置换
	}

	public static void main(String[] args) {
		//例子测试
		LC146 lc146 = new LC146(6);
		for (int i = 1; i <= 6; i++) {
			lc146.put(i, i);
		}
		lc146.get(1); //调用了1，所以最久未使用变成[2,2]
		lc146.put(7, 7); //空间只有6，此时会置换出最久没使用的，即[2,2]
		System.out.println(lc146.entrySet().toString());
	}
}
```

## 155. 最小栈 ##

> 设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。
>
> + push(x) —— 将元素 x 推入栈中。
> + pop() —— 删除栈顶的元素。
> + top() —— 获取栈顶元素。
> + getMin() —— 检索栈中的最小元素。

```css
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

### 思路分析 ###

+ 前面三个功能都是栈的基本功能，第四个功能需要自行设计
+ 我们可以使用一个辅助栈存放添加进当前元素时候的最小值，辅助栈初始值为Max
+ 栈的结构当然可以使用`Stack`进行模拟，不过`Deque【LinkedList实现的一个接口】`是更好的选择，因为其可以兼顾头尾两侧

### 代码实现 ###

```java
class MinStack {
    Deque<Integer> xStack;
    Deque<Integer> minStack;

    public MinStack() {
        xStack = new LinkedList<Integer>();
        minStack = new LinkedList<Integer>();
        minStack.push(Integer.MAX_VALUE);
    }
    
    public void push(int x) {
        xStack.push(x);
        minStack.push(Math.min(minStack.peek(), x));
    }
    
    public void pop() {
        xStack.pop();
        minStack.pop();
    }
    
    public int top() {
        return xStack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

# 十五、深搜/广搜 #

## 200. 岛屿数量 ##

> 给你一个由`1`（陆地）和 `0`（水）组成的的二维网格，请你计算网格中岛屿的数量。<br>岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。<br>此外，你可以假设该网格的四条边均被水包围。

```css
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

### 思路分析 ###

+ 每个岛屿都是被"水"包围起来的，换句话说，**两块陆地之间只要有路能够到达，那就算是一块陆地**。由此我们可以想到**深度优先遍历**，一直往下探，直到没路为止，这当然属于一块岛屿
+ 具体实现是对二维数组进行遍历，当发现有陆地时，就以当前陆地为出发点进行上下左右四个方向的深度遍历，每到一个地方就将`1`变为`0`，防止走回头路
+ 当我们对一块陆地深度遍历后，就会将与此陆地属于同一岛屿的陆地都标记为`0`，此时计数器`+1`，表示岛屿数为`1`

### 代码实现 ###

```java
class Solution {
    int row;
	int coloum;

	public int numIslands(char[][] grid) {
		int num=0;
		row = grid.length; // 行
		coloum = grid[0].length; // 列
		for (int i = 0; i < row; i++) {
			for (int j = 0; j < coloum; j++) {
				if(grid[i][j]=='1') {
					judge(grid, j, i);
					num++;
				}
			}
		}
		return num;
	}

	public void judge(char[][] grid, int x, int y) { //x是列，y是行
		if (x >= coloum || y >= row) {
			return;
		}
		grid[y][x]='0';
		if (x + 1 < coloum && grid[y][x + 1] == '1') { //走下
			judge(grid, x + 1, y);
		}
		if (y + 1 < row && grid[y + 1][x] == '1') { //走右
			judge(grid, x, y + 1);
		}
		if(x-1>=0&&grid[y][x-1]=='1') { //走左
			judge(grid, x-1, y);
		}
		if(y-1>=0&&grid[y-1][x]=='1') { //走左
			judge(grid, x, y-1);
		}		
	}
}
```

# 十六、图 #

## 207. 课程表 ##

> 你这个学期必须选修 `numCourses` 门课程，记为 `0` 到 `numCourses - 1` 。
>
> 在选修某些课程之前需要一些先修课程。 先修课程按数组 `prerequisites` 给出，其中 `prerequisites[i] = [ai, bi] `，表示如果要学习课程 `ai` 则 必须 先学习课程  `bi` 。
>
> 例如，先修课程对 `[0, 1]` 表示：想要学习课程 `0` ，你需要先完成课程 `1` 。
> 请你判断是否可能完成所有课程的学习？如果可以，返回 `true` ；否则，返回 `false` 。

```css
输入：numCourses = 2, prerequisites = [[1,0],[0,1]]
输出：false
解释：总共有 2 门课程。学习课程 1 之前，你需要先完成课程 0 ；并且学习课程 0 之前，你还应先完成课程 1 。这是不可能的。
```

### 思路分析 ###

+ 在有向图中，指向节点的线个数叫做此节点的**入度**，离开此节点的线个数叫做此节点的**出度**
+ 我们可以将整个课程表化成两个哈希表，`map<Integer,Integer>`负责存放当前课程对应的入度；`adj<Integer,List<Integer>>`负责存放**依赖于当前课程的后续课程**
+ 初始时，所有课程的入度都是`0`，对二维数组进行扫描，当某一课程有先修课程时，这一课程的入度就`+1`，并且将这一个课程加入先修课程对应的`List`中，说明这一课程是依赖于先修课程的
+ 创建一队列`queue`用来存放度为`0`的课程，对其进行遍历：将此课程对应的`List`全部得到，其元素对应的入度全部`-1`，因为此时即将少一个前提课程。如果`-1`后发现入度为`0`，说明这个课程可以直接学习了，将其放入队列。
+ 在一个课程学习完毕后，将其从两个哈希表中移除，最后`map`哈希表若为空，说明所有课程都学习到了，返回`true`，否则返回`false`

### 代码实现 ###

```java
public boolean canFinish(int numCourses, int[][] prerequisites) {
	Map<Integer, Integer> map = new HashMap<Integer, Integer>(); // 每个课程对应的入度
		Map<Integer, List<Integer>> adj = new HashMap<Integer, List<Integer>>();// 依赖于此课程的后续课程
		for (int i = 0; i < numCourses; i++) {
			map.put(i, 0); // 初始化所有课程入度
		}
		// 完善两个哈希表内容
		for (int[] is : prerequisites) {
			int cur = is[0]; // 当前课程
			int pre = is[1];// 前提课程
			map.put(cur, map.get(cur) + 1); // 将当前课程入度+1，因为其有前提课程
			if (!adj.containsKey(pre)) {
				// 如果当前没有依赖于此前提课程的后续课程，则需要开辟一个空间，准备放入一个元素
				adj.put(pre, new ArrayList<>());
			}
			adj.get(pre).add(cur); // 将当前课程加入到前提课程的后续课程中
		}
	Queue<Integer> queue = new LinkedList<>(); // 存放入度为0的元素，即没有前缀课程的课程
		for (Integer key : map.keySet()) {
			if (map.get(key) == 0) {
				queue.offer(key);
			}
		}
		// 队列不空，说明有入度为0的课程没有处理
		while (!queue.isEmpty()) {
			int head = queue.poll(); // 获得队列头部元素
			if (!adj.containsKey(head)) { // 说明当前没有依赖当前课程的课程
				map.remove(head);
				continue;
			}
			List<Integer> list = adj.get(head);// 获得依赖于当前课程的课程
			for (Integer index : list) {
				map.put(index, map.get(index) - 1); // 当前入度为0，即将被抹去，所以依赖于它的课程入度也-1
				if (map.get(index) == 0) { // 如果当前的课程入度为0，说明其可以学习了，放入队列
					queue.offer(index);
				}
			}
			adj.remove(head); // 当前课程学习完毕，将其从映射中去除
			map.remove(head);
		}
		if (map.size() == 0) {
			return true;
		}
		return false;
	}
```

# 十七、字典树 #

## 208. 实现Trie(前缀树) ##

> 实现一个 Trie (前缀树)，包含 `insert`, `search`, 和 `startsWith` 这三个操作。
>
> + 你可以假设所有的输入都是由小写字母 `a-z` 构成的。
> + 保证所有输入均为非空字符串。

```css
Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // 返回 true
trie.search("app");     // 返回 false
trie.startsWith("app"); // 返回 true
trie.insert("app");   
trie.search("app");     // 返回 true
```

### 思路分析 ###

+ 所谓前缀树，其实就是**字典树**，是一种常用的数据结构，通常用于输入法提示等

+ 由一个根节点引导，每个节点向下预留26个节点位置，并且每个节点需要有标志位来验证是否有单词在此节点结束。我们人为构造这么一个节点

  ```java
  class TrieNode {
  		TrieNode[] next = new TrieNode[26]; // 当前节点下面接26个字母
  		boolean End = false; // 当前节点是否为某个单词结尾
  
  		public boolean isEnd() {
  			return End;
  		}
  
  		public void setEnd(boolean flag) {
  			End = flag;
  		}
  	}
  ```

+ 当进行插入操作时，只需要根据传入的字符顺序一步步往下构建，并且将最后一个字符所在节点位置标记为`true`，以便后期进行搜索操作

+ 当进行搜索操作时候，只需要根据传入的字符一步步往下验证，如果有遇到不符合的则返回`false`，说明字典里没有这个字符串，如果一路符合，则在最后面还需要验证最后一个节点的`End`是否为`true`，如果为`true`说明字典里确实是有这个字符串；若为`false`说明这个字符串在整个字典里只充当子集，没有这样的单词。

+ 当进行前缀验证操作时，只需要根据传入的字符一步步往下验证，如果有遇到不符合的则返回`false`，说明字典里没有这个前缀。所有字符都扫描一遍后没有退出，说明这个前缀在字典中是存在的，所以返回`true`

### 代码实现 ###

```java
public class LC208 {
	TrieNode root; // 根节点

	class TrieNode {
		TrieNode[] next = new TrieNode[26]; // 当前节点下面接26个字母
		boolean End = false; // 当前节点是否为某个单词结尾

		public boolean isEnd() {
			return End;
		}

		public void setEnd(boolean flag) {
			End = flag;
		}
	}

	public LC208() {
		root = new TrieNode();// 初始化根节点
	}

	// 插入方法
	public void insert(String word) {
		char[] array = word.toCharArray(); // 将字符串转换成字符数组
		TrieNode temp = root;
		for (int i = 0; i < array.length; i++) {
			int ch = array[i] - 'a'; // 得到当前字符对应的下标
			if (temp.next[ch] == null) { // 说明当前还没有存入过这个字符
				temp.next[ch] = new TrieNode(); // 创建这个字符
			}
			temp = temp.next[ch]; // 指针往下走
		}
		temp.setEnd(true); // 说明当前位置有单词在这里结束
	}

	// 搜索方法
	public boolean search(String word) {
		char[] array = word.toCharArray(); // 将字符串转换成字符数组
		TrieNode temp = root;
		for (int i = 0; i < array.length; i++) {
			int ch = array[i] - 'a';
			if (temp.next[ch] == null) {
				return false;
			}
			temp = temp.next[ch];
		}
		return temp.isEnd();
	}

	// 是否以某字符串开头
	public boolean startsWith(String prefix) {
		char[] array = prefix.toCharArray(); // 将字符串转换成字符数组
		TrieNode temp = root;
		for (int i = 0; i < array.length; i++) {
			int ch = array[i] - 'a';
			if (temp.next[ch] == null) {
				return false;
			}
			temp = temp.next[ch];
		}
		return true;
	}
}
```

# 十八、前缀和 #

## 437. 路径总和III ##

> 给定一个二叉树，它的每个结点都存放着一个整数值。<br>找出路径和等于给定数值的路径总数。<br>路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。<br>二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

```css
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8
      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1
返回 3。和等于 8 的路径有:
1.  5 -> 3
2.  5 -> 2 -> 1
3.  -3 -> 11
```

### 思路分析 ###

+ 我们利用**前缀和**的方式解决这道题，即如果当前累加的总和为`currSum`，如果要出现一条`target`的路径，那么我肯定要**先**到得了`currSum-target`这个值，**这个起点位置与我终点位置的距离正好为`target`**
+ 那么我如何知道自己到不到得了`currSum-target`这个位置呢？首先，由于我们的路是一棵树，因此只有节点相加的值才是我们到得了的地方，我们用一个`map`来**记录到达某个值(key)的次数(value)**
+ 我们有几条路径`res`取决于`currSum-target`这个值有几个，即`int res = map.getOrDefault(currSum - target, 0)`，由于自己到自己肯定有一条路径，所以提前设置`map.put(0, 1)`。
+ 更新`map`，放入最新的前缀值
+ 接着往左右两边走看看各自都有多少路径，左右的路径加上自己这里的路径就是当前能够到达`target`的总路径。**注意：由于左右不互通，因此左边前缀值不应该影响到右边前缀值的判断，所以在一个节点在完成使命后需要抹除自己记录在`map`中的前缀和信息，避免混乱**

### 代码实现 ###

```java
public int pathSum(TreeNode root, int sum) {
		// key是前缀和, value是大小为key的前缀和出现的次数
		Map<Integer, Integer> prefixSumCount = new HashMap<>();
		// 前缀和为0的一条路径
		prefixSumCount.put(0, 1);
		// 前缀和的递归回溯思路
		return recursionPathSum(root, prefixSumCount, sum, 0);
	}

	private int recursionPathSum(TreeNode node, Map<Integer, Integer> prefixSumCount, int target, int currSum) {
		// 1.递归终止条件
		if (node == null) {
			return 0;
		}
		// 当前路径上的和
		currSum = currSum + node.val; // 遍历到当前位置时的总和
		// 判断当前点是否有路径：如果我能有到curr-target的路，那我当然会有到target的路
		int res = prefixSumCount.getOrDefault(currSum - target, 0);
		// 当前总数如果已经出现过，则在原来基础上+1；若未出现，说明这是第一次出现，令其为1
		prefixSumCount.put(currSum, prefixSumCount.getOrDefault(currSum, 0) + 1);
		// 看看左边有多少条路
		res = res + recursionPathSum(node.left, prefixSumCount, target, currSum);
		// 看看右边有多少路
		res = res + recursionPathSum(node.right, prefixSumCount, target, currSum);
		// 由于左边和右边是没有通路的，因此为了不让左边的前缀和影响到右边
		// 因此处理完后要将所对应的前缀和从哈希表中删去
		prefixSumCount.put(currSum, prefixSumCount.get(currSum) - 1);
		return res;
	}
```

## 560. 和为K的子数组 ##

> 给定一个整数数组和一个整数 **k，**你需要找到该数组中和为 **k** 的连续的子数组的个数。
>
> 1. 数组的长度为 [1, 20,000]。
> 2. 数组中元素的范围是 [-1000, 1000] ，且整数 **k** 的范围是 [-1e7, 1e7]。

```css
输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
```

### 思路分析 ###

+ 首先数组元素允许为负数，因此**不能用滑动窗口**右边扩大、左边缩小的方法
+ 面对这种情况我们可以考虑**前缀和**，按顺序算下来的两个**前缀和之差`(pre2-pre1)`**就是夹在中间部分元素之和，如果这部分等于`K`，那我们就解决问题了。难点在于：当``前缀和pre2`前面有好多个`前缀和pre1`，我们怎么知道哪个`pre2`到`pre1`的差值正好是`K`呢？
+ 答案是**利用减法**，因为我们知道当前终点`pre2`，也知道差值`K`，所以`pre1=pre2-K`。接着我们再利用哈希表将扫描过的前缀和都记录起来，所以其实我们得到`pre1`后只需要在哈希表中进行检索，就可以知道前缀值为`pre1`出现了多少次，而这个就是**和为K的子数组的次数**

### 代码实现 ###

```java
public int subarraySum(int[] nums, int k) {
		int count = 0;
		Map<Integer, Integer> map = new HashMap<Integer, Integer>(); // (前缀和，次数)
		map.put(0, 1);
		int pre = 0;
		for (int i = 0; i < nums.length; i++) {
			pre += nums[i];
			count += map.getOrDefault(pre - k, 0);
			map.put(pre, map.getOrDefault(pre, 0) + 1);
		}
		return count;
	}
```
