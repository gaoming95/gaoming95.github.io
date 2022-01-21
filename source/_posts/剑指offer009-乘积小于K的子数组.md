---
title: 剑指offer009-乘积小于K的子数组
subtitle: 乘积小于K的子数组
date: 2021-11-03
author: 高明
tags:
	- 剑指offer
---



# 剑指offer009-乘积小于K的子数组

## 题目
<p>给定一个正整数数组&nbsp;<code>nums</code>和整数 <code>k</code>&nbsp;，请找出该数组内乘积小于&nbsp;<code>k</code>&nbsp;的连续的子数组的个数。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入:</strong> nums = [10,5,2,6], k = 100
<strong>输出:</strong> 8
<strong>解释:</strong> 8 个乘积小于 100 的子数组分别为: [10], [5], [2], [6], [10,5], [5,2], [2,6], [5,2,6]。
需要注意的是 [10,5,2] 并不是乘积小于100的子数组。
</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong> nums = [1,2,3], k = 0
<strong>输出:</strong> 0</pre>

<p>&nbsp;</p>

<p><strong>提示:&nbsp;</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 3 * 10<sup>4</sup></code></li>
	<li><code>1 &lt;= nums[i] &lt;= 1000</code></li>
	<li><code>0 &lt;= k &lt;= 10<sup>6</sup></code></li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 713&nbsp;题相同：<a href="https://leetcode-cn.com/problems/subarray-product-less-than-k/">https://leetcode-cn.com/problems/subarray-product-less-than-k/</a>&nbsp;</p>
<div><div>Related Topics</div><div><li>数组</li><li>滑动窗口</li></div></div><br><div><li>👍 22</li><li>👎 0</li></div>

## 思路

没啥思路

## 代码

```java
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if (k == 0) {
            return 0;
        }

        int res = 0;
        for (int i = 0; i < nums.length; i++) {
            int s = 1;
            for (int j = i; j < nums.length; j++) {
                s = s * nums[j];
                if (s >= k) {
                    break;
                }
                res = res + 1;
            }
        }
        return res;
    }
}
```

```
解答成功:
执行耗时:872 ms,击败了12.85% 的Java用户
内存消耗:45.2 MB,击败了78.44% 的Java用户
```

