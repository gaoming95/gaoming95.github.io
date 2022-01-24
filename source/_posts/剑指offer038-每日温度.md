---
title: 剑指offer038-每日温度
subtitle: 每日温度
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer038-每日温度

## 题目

<p>请根据每日 <code>气温</code> 列表 <code>temperatures</code>&nbsp;，重新生成一个列表，要求其对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用&nbsp;<code>0</code> 来代替。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入:</strong> <code>temperatures</code> = [73,74,75,71,69,72,76,73]
<strong>输出:</strong>&nbsp;[1,1,4,2,1,1,0,0]
</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong> temperatures = [30,40,50,60]
<strong>输出:</strong>&nbsp;[1,1,1,0]
</pre>

<p><strong>示例 3:</strong></p>

<pre>
<strong>输入:</strong> temperatures = [30,60,90]
<strong>输出: </strong>[1,1,0]</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;=&nbsp;temperatures.length &lt;= 10<sup>5</sup></code></li>
	<li><code>30 &lt;=&nbsp;temperatures[i]&nbsp;&lt;= 100</code></li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 739&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/daily-temperatures/">https://leetcode-cn.com/problems/daily-temperatures/</a></p>
<div><div>Related Topics</div><div><li>栈</li><li>数组</li><li>单调栈</li></div></div><br><div><li>👍 21</li><li>👎 0</li></div>

## 思路

这道题，理解下来就是在数组中，找到每一个位置右边第一个比它大的数

一开始的想法，暴力，对每一个位置遍历，找到右边第一个比它大的数即可，时间复杂度O(n^2)

再仔细想想，就会往单调栈的方向走了（题目做多了）

考虑单调增还是单调减的问题，如果维护一个递增的序列，是单调减栈，它是从栈顶往栈底的方向考虑的，具体名词我们先不考虑

其实在矩形最大面积或者接雨水题目中，我们通过维护一个递增关系，当需要破环这个关系时，栈中元素逐个出栈，直到满足要求

这道题我们需要维护一个递减关系，因为右边的值只要小于当前值，其实对当前值的右侧第一个大值是没有帮助的

比如 `73,74,75,71,69,72,76,73`

1. 73入栈
2. 74比73大，73出栈，计算二者的区间距离，74入栈
3. 75比74大，74出栈，计算二者的区间距离，75入栈
4. 71比75小，入栈
5. 69比75小，入栈
6. 72比69大，出栈，计算距离更新
7. 72比71大，出栈，计算距离更新

首先这种题目，先写框架

```java
Stack<Integer> stack = new Stack<>();
stack.push(-1);
for (int i = 0; i < temperatures.length; i++) {
    stack.push(i);
}
```

遍历数组，直接入栈

开始判断如果出现破坏条件的情况

```java
while (stack.peek() != -1 && temperatures[i] > temperatures[stack.peek()]) {
    Integer index = stack.pop();
    res[index] = i - index;
}
```

## 代码

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        Stack<Integer> stack = new Stack<>();
        stack.push(-1);
        int[] res = new int[temperatures.length];
        for (int i = 0; i < temperatures.length; i++) {
            while (stack.peek() != -1 && temperatures[i] > temperatures[stack.peek()]) {
                Integer index = stack.pop();
                res[index] = i - index;
            }
            stack.push(i);
        }
        return res;
    }
}
```

```
解答成功:
执行耗时:40 ms,击败了28.14% 的Java用户
内存消耗:47.7 MB,击败了65.49% 的Java用户
```



