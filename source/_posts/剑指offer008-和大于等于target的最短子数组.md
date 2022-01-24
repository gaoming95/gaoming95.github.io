---
title: 剑指offer008-和大于等于target的最短子数组
subtitle: 和大于等于target的最短子数组
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer008-和大于等于target的最短子数组

## 题目
<p>给定一个含有&nbsp;<code>n</code><strong>&nbsp;</strong>个正整数的数组和一个正整数 <code>target</code><strong> 。</strong></p>

<p>找出该数组中满足其和<strong> </strong><code>&ge; target</code><strong> </strong>的长度最小的 <strong>连续子数组</strong>&nbsp;<code>[nums<sub>l</sub>, nums<sub>l+1</sub>, ..., nums<sub>r-1</sub>, nums<sub>r</sub>]</code> ，并返回其长度<strong>。</strong>如果不存在符合条件的子数组，返回 <code>0</code> 。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>target = 7, nums = [2,3,1,2,4,3]
<strong>输出：</strong>2
<strong>解释：</strong>子数组&nbsp;<code>[4,3]</code>&nbsp;是该条件下的长度最小的子数组。
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>target = 4, nums = [1,4,4]
<strong>输出：</strong>1
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>target = 11, nums = [1,1,1,1,1,1,1,1]
<strong>输出：</strong>0
</pre>

<p>&nbsp;</p>

<p>提示：</p>

<ul>
	<li><code>1 &lt;= target &lt;= 10<sup>9</sup></code></li>
	<li><code>1 &lt;= nums.length &lt;= 10<sup>5</sup></code></li>
	<li><code>1 &lt;= nums[i] &lt;= 10<sup>5</sup></code></li>
</ul>

<p>&nbsp;</p>

<p>进阶：</p>

<ul>
	<li>如果你已经实现<em> </em><code>O(n)</code> 时间复杂度的解法, 请尝试设计一个 <code>O(n log(n))</code> 时间复杂度的解法。</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 209&nbsp;题相同：<a href="https://leetcode-cn.com/problems/minimum-size-subarray-sum/">https://leetcode-cn.com/problems/minimum-size-subarray-sum/</a></p>
<div><div>Related Topics</div><div><li>数组</li><li>二分查找</li><li>前缀和</li><li>滑动窗口</li></div></div><br><div><li>👍 19</li><li>👎 0</li></div>

## 思路

### 思路一

一开始的想法有一点动态规划的感觉，后面感觉没必要，两个循环，分别计算从`i~j`的和`s`，如果`s>target`，记录一下`j-i+1`，求最小的距离就好。因为是正整数，所以当`s>target`的时候，就不需要计算后面的和了，很容易写出代码

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int res = Integer.MAX_VALUE;

        for (int i = 0; i < nums.length; i++) {
            int s = 0;
            for (int j = i; j < nums.length; j++) {
                s += nums[j];
                if (s >= target) {
                    res = Math.min(j - i + 1, res);
                    break;
                }
            }
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

### 思路二

这种连续数组应该想到双指针，左右指针都指向开始的位置即`left = right = 0`，初始和为`nums[0]`

每次计算的时候，如果求和`s<target`，`right++`，如果求和`s>=target`，`left++`，并且记录当时的下标值

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int right = 0;
        int s = nums[0];
        int res = Integer.MAX_VALUE;
        while (right < nums.length) {
            if (s < target) {
                right += 1;
                if (right < nums.length) {
                    s += nums[right];
                }

            } else {
                s -= nums[left];
                res = Math.min(res, right - left + 1);
                left += 1;
            }
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```



## 代码

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int res = Integer.MAX_VALUE;

        for (int i = 0; i < nums.length; i++) {
            int s = 0;
            for (int j = i; j < nums.length; j++) {
                s += nums[j];
                if (s >= target) {
                    res = Math.min(j - i + 1, res);
                    break;
                }
            }
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

```
解答成功:
执行耗时:135 ms,击败了5.93% 的Java用户
内存消耗:38.4 MB,击败了42.34% 的Java用户
```

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int right = 0;
        int s = nums[0];
        int res = Integer.MAX_VALUE;
        while (right < nums.length) {
            if (s < target) {
                right += 1;
                if (right < nums.length) {
                    s += nums[right];
                }

            } else {
                s -= nums[left];
                res = Math.min(res, right - left + 1);
                left += 1;
            }
        }
        return res == Integer.MAX_VALUE ? 0 : res;
    }
}
```

```
解答成功:
执行耗时:1 ms,击败了99.82% 的Java用户
内存消耗:38.5 MB,击败了18.45% 的Java用户
```

