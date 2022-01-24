---
title: 剑指offer003-前n个数字二进制中1的个数
subtitle: 前n个数字二进制中1的个数
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer003-前n个数字二进制中1的个数

## 题目

<p>给定一个非负整数 <code>n</code><b>&nbsp;</b>，请计算 <code>0</code> 到 <code>n</code> 之间的每个数字的二进制表示中 1 的个数，并输出一个数组。</p>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入: </strong>n =<strong> </strong>2
<strong>输出: </strong>[0,1,1]
<strong>解释: 
</strong>0 --&gt; 0
1 --&gt; 1
2 --&gt; 10
</pre>

<p><strong>示例&nbsp;2:</strong></p>

<pre>
<strong>输入: </strong>n =<strong> </strong>5
<strong>输出: </strong><code>[0,1,1,2,1,2]
</code><span style="white-space: pre-wrap;"><strong>解释:</strong>
</span>0 --&gt; 0
1 --&gt; 1
2 --&gt; 10
3 --&gt; 11
4 --&gt; 100
5 --&gt; 101
</pre>
<p><strong>说明 :</strong></p>

<ul>
	<li><code>0 &lt;= n &lt;= 10<sup>5</sup></code></li>
</ul>
<p><strong>进阶:</strong></p>

<ul>
	<li>给出时间复杂度为&nbsp;<code>O(n*sizeof(integer))</code><strong>&nbsp;</strong>的解答非常容易。但你可以在线性时间&nbsp;<code>O(n)</code><strong>&nbsp;</strong>内用一趟扫描做到吗？</li>
	<li>要求算法的空间复杂度为&nbsp;<code>O(n)</code>&nbsp;。</li>
	<li>你能进一步完善解法吗？要求在C++或任何其他语言中不使用任何内置函数（如 C++ 中的&nbsp;<code>__builtin_popcount</code><strong>&nbsp;</strong>）来执行此操作。</li>
</ul>
## 思路

很明显的动态规划的题目，先分析规律

```
n	二进制		1的数量
0		0		0
1		1		1
-------------------
2		10		1
3		11		2
-------------------
4		100		1	-> n=0 (+1)
5		101		2	-> n=1 (+1)
6		110		2	-> n=2 (+1)
7		111		3	-> n=3 (+1)
-------------------
8		1000	1	-> n=0 (+1)
9		1001	2	-> n=1 (+1)
10		1010	2	-> n=2 (+1)
11		1011	3	-> n=3 (+1)
12		1100	2
13		1101	3
14		1110	3
15		1111	4
------------------
16		10000	1
```

动态规划一般是找A[i]和A[i-1]之间的关系，很容易找出规律，比如4-7，把二进制的最高位和后面两位拆分，发现实际上就是0-3的1的数量+1。

比如8-15，实际上就是0-7的1的个数+1

因此，当我们初始化数组，`a[0] = 0, a[1] = 1`时

我们发现`a[n]`实际上应该等于`a[n-小于n的最小2的幂] + 1`

即`a[5] = a[5-4] + 1`，`a[15] = a[15-8] + 1` 

本质上说，当二进制的位数上升之后，最高位肯定是1，剩下的位数我们可以在动态数组中找到

即4位的二进制，最高位肯定时1，剩下的三位中1的个数可由三位二进制得到，形成递推关系式

## 代码

```java
class Solution {
    public int[] countBits(int n) {
        if(n == 0){
            return new int[1];
        }
        int[] res = new int[n + 1];
        res[0] = 0;
        res[1] = 1;
        for (int i = 2; i < n + 1; i++) {
            res[i] = res[largest_power(i)] + 1;
        }
        return res;
    }

    int largest_power(int N) {
        int N1 = N;
        N = N | (N >> 1);
        N = N | (N >> 2);
        N = N | (N >> 4);
        N = N | (N >> 8);
        N = N | (N >> 16);
        int maxPower = (N + 1) >> 1;
        return N1 & ~maxPower;
    }
}
```

```
解答成功:
执行耗时:2 ms,击败了39.71% 的Java用户
内存消耗:42.3 MB,击败了75.21% 的Java用户
```

