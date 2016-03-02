---
layout: post
title: LeetCode刷题笔记...不断更新ing
category: [经验积累]
date: 2016-02-29
---
2016.2.28日开始刷LeetCode算法题，预计用Java和Python各刷一遍。

本文用于记录刷题过程中的思考及对于答案的整理，来更好的了解这些算法。

以下记录按照刷题的顺序。
<!-- more -->


# 292. Nim Game

You are playing the following Nim Game with your friend: There is a heap of stones on the table, each time one of you take turns to remove 1 to 3 stones. The one who removes the last stone will be the winner. You will take the first turn to remove the stones.

Both of you are very clever and have optimal strategies for the game. Write a function to determine whether you can win the game given the number of stones in the heap.

For example, if there are 4 stones in the heap, then you will never win the game: no matter 1, 2, or 3 stones you remove, the last stone will always be removed by your friend.

你和朋友一起玩鼎鼎大名的Nim游戏，规则是这样的：桌子上有一堆的石头，你们俩轮流拿走石头，每次只能拿走1到3块，拿走最后一块石头的人赢，游戏从你先拿开始。

你们两个都是非常聪明并对于这个游戏有最佳策略，写程序来判断：当桌子上有一定数量的石头时你是否会赢。

举例来说，如果桌子上有4块石头，那么你永远不会赢：无论你首先拿1、2还是3块，最后一块石头总是被你的朋友拿走。


## 思考:
  给的例子很有参考价值，当桌子上剩余4块石头时候，你永远不会赢，而每轮两人最多拿走6块石头。所以无论前期怎样从桌子上拿石头，最后一定会剩余1~6块石头（如果剩余的石头大于6块，比如8块，那还可以再拿一轮，一轮最多拿6块，最少拿2块，那一定会剩余2~6块，仍在推断的1~6块范围内）。这样的话把无穷个结果的问题就分解成了有限解，分类讨论后得出结论，剩余1~3块可以直接拿走赢下比赛，5~6块也有必胜的策略，唯独有4块的时候是必输的。那就变成了谁先给对方留下4块石头，谁就赢了，分析找一下规律就能得出仅当石头数量是4的倍数时，游戏是必输的，其它情况下都能赢。


## 代码:
JAVA:
{% highlight java %}
public class Solution {
    public boolean canWinNim(int n) {
		return n % 4 != 0;
    }
}
{% endhighlight %}

# 136. Single Number
Given an array of integers, every element appears twice except for one. Find that single one.

Note:
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

有一个整数数组，除了一个元素之外，每个元素都出现了两遍。找出那个单独的元素。

注意：你的算法应当是线性时间的，你能不用额外的存储空间实现它吗？


## 思考:
1.最省心的方法是套两遍循环，就能够求出单独的元素，但是时间复杂度是O(n*n)，不符合，倒是也不需要额外的存储空间。
2.除此之外可以先进行一遍排序，这样的话单独的元素一定会出现在数组的奇数坐标下（即偶数下标，比如a[0],a[2]……），那只要判断元素和其相邻元素是否相同就能得到单独元素了。


## 代码:
JAVA:(原始版本)
{% highlight java %}
public class Solution {
    public int singleNumber(int[] nums) {
	int number = 0;
	Arrays.sort(nums);
	if(nums.length == 1){
	    number = nums[0];
	}
	else{
	    for(int i = 0;i < nums.length - 1; i+=2){
	        if(nums[i] != nums[i+1]){
	            number = nums[i];
	            break;
	        }
	        else
	            number = nums[nums.length-1];
	    }
	}
	return number;
    }
}
{% endhighlight %}

JAVA:(更新后)
{% highlight java %}
public class Solution {
    public int singleNumber(int[] nums) {
	Arrays.sort(nums);
	for(int i = 0;i < nums.length ; i+=2){
	    if(nums.length == i + 1 || nums[i] != nums[i+1]){
			return nums[i];
		}
	}
	    return 0;
    }
}
{% endhighlight %}


## 参考:
给出的既符合线性时间又符合不使用额外元素的最佳算法是：使用异或XOR。
因为XOR适用交换律和结合律（ 1^2^3^2^3^4^1 = (1^1)^(2^2)^(3^3)^4 = 0^0^0^0^4 = 4 )，所以将数组内所有元素执行XOR即可返回最终的结果。
	
	
JAVA:
{% highlight java %}
public class Solution {
    public int singleNumber(int[] nums) {
	for(i = 1;i <= nums.length; i++){
		nums[i] ^= nums[i-1];
		return nums[nums.length-1];
    }
}
{% endhighlight %}

# 258.Add Digits
Given a non-negative integer num, repeatedly add all its digits until the result has only one digit.

For example:

Given num = 38, the process is like: 3 + 8 = 11, 1 + 1 = 2. Since 2 has only one digit, return it.

Follow up:
Could you do it without any loop/recursion in O(1) runtime?

给予一个非负整数num，将其各个数位上的数相加，直到结果为一个数字。
例如：num = 38 ，过程将会是： 3 + 8 = 11, 1 + 1 =2。而2已经是一个数字了，所以2就是返回值。
拓展：能否不用循环/递归且运行时间复杂度为O(1)？

## 思考：
按照基础步骤来，不管拓展先得到一个正确结果。普遍的做法就是两层循环，外层用来控制每次得到的新结果能够再进入内层循环，内层循环来累加数位，直到累加结果为个位数（小于10），就可以返回了。

## 代码:
JAVA:
{% highlight java %}
public class Solution {
    public int addDigits(int num) {
        while(true){
            int result = 0;
            while(num != 0){
                result += num % 10;
                num = num / 10;
            }
            if(result >= 10){
                num = result;
            }
            else{
                return result;
            }
        }
    }
}
{% endhighlight %}

## 参考:
看了更有技巧的答案后恍然大悟,取任意一个六位数来举例：

y = 100000 * a + 10000 * b + 1000 * c + 100 * d + 10 * e + f , 其中abcdef为个位上的数字。

y = (a + b + c + d + e + f) + (99999 * a + 9999 * b + 999 * c + 99 * d + 9 * e) , 而后边括号是可以被9整除的。

若 a + b + c + d + e >= 10 , 可以继续按照这个方法，最后总能得到一个 属于(0,9]的number +　一堆可以被9整除的数。

y % 9 = (m + n) % 9 = (m % 9 + n % 9) % 9 = m	

此处有一个需要注意的地方是，当number为9的时候，最后的结果就应该9，而如果按照以上算法来计算，那得出的结果就会是0，很显然是不对的。

将以上结果是9的情况过滤掉，就能得到算法：

JAVA:
{% highlight java %}
public class Solution {
    public int addDigits(int num) {
        return (num - 1) % 9 + 1;
    }
}
{% endhighlight %}
