---
title: 剑指offer007-数组中和为0的三个数
subtitle: 数组中和为0的三个数
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer007-数组中和为0的三个数

## 题目

<p>给定一个包含 <code>n</code> 个整数的数组&nbsp;<code>nums</code>，判断&nbsp;<code>nums</code>&nbsp;中是否存在三个元素&nbsp;<code>a</code> ，<code>b</code> ，<code>c</code> <em>，</em>使得&nbsp;<code>a + b + c = 0</code> ？请找出所有和为 <code>0</code> 且&nbsp;<strong>不重复&nbsp;</strong>的三元组。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>nums = [-1,0,1,2,-1,-4]
<strong>输出：</strong>[[-1,-1,2],[-1,0,1]]
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>nums = []
<strong>输出：</strong>[]
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>nums = [0]
<strong>输出：</strong>[]
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>0 &lt;= nums.length &lt;= 3000</code></li>
	<li><code>-10<sup>5</sup> &lt;= nums[i] &lt;= 10<sup>5</sup></code></li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 15&nbsp;题相同：<a href="https://leetcode-cn.com/problems/3sum/">https://leetcode-cn.com/problems/3sum/</a></p>
<div><div>Related Topics</div><div><li>数组</li><li>双指针</li><li>排序</li></div></div><br><div><li>👍 23</li><li>👎 0</li></div>

## 思路

一开始想的就是三个for循环，计算三数之和是否等于0，这种肯定超出时间限制

后面的想的就是双指针，固定一个数不变，双指针寻找另外两个数字，但是有一点就是重复的数字处理，开始的数字大于0的需要被过滤掉

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> res = new ArrayList<>();
        HashSet<String> set = new HashSet<>();
        for (int i = 0; i < nums.length; i++) {

            if (nums[i] > 0) {
                break;
            }

            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }

            int left = i + 1;
            int right = nums.length - 1;
            while (left < right) {
                if (left < 0) {
                    return res;
                }
                int t = nums[left] + nums[right];
                if (t + nums[i] > 0) {
                    right -= 1;
                } else if (t + nums[i] < 0) {
                    left += 1;
                } else {
                    String key = "" + nums[i] + nums[left] + nums[right];
                    if (!set.contains(key)) {
                        res.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    }
                    set.add(key);

                    while (left < right && nums[left] == nums[left + 1]) {
                        left++;
                    }

                    left += 1;
                }
            }
        }
        return res;
    }
}
```



