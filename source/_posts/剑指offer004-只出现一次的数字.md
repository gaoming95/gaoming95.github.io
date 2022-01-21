---
title: 剑指offer004-只出现一次的数字
subtitle: 只出现一次的数字
date: 2021-11-02
author: 高明
tags:
	- 剑指offer
---



# 剑指offer004-只出现一次的数字

## 题目

<p>给你一个整数数组&nbsp;<code>nums</code> ，除某个元素仅出现 <strong>一次</strong> 外，其余每个元素都恰出现 <strong>三次 。</strong>请你找出并返回那个只出现了一次的元素。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>nums = [2,2,3,2]
<strong>输出：</strong>3
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>nums = [0,1,0,1,0,1,100]
<strong>输出：</strong>100
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 3 * 10<sup>4</sup></code></li>
	<li><code>-2<sup>31</sup> &lt;= nums[i] &lt;= 2<sup>31</sup> - 1</code></li>
	<li><code>nums</code> 中，除某个元素仅出现 <strong>一次</strong> 外，其余每个元素都恰出现 <strong>三次</strong></li>
</ul>

<p>&nbsp;</p>

<p><strong>进阶：</strong>你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？</p>

<p>&nbsp;</p>

## 思路

首先这个题目一看就是有什么简便方法，比如位操作，可以消除两个重复出现的数字`（1，1，2，2，3）`，比如这中，除了一个数字个数为1，其余个数为偶数，就可以按位操作

最正常的思路应该是计数，用Hash表存储每一个数字key出现的次数，统计次数为1的key返回

```java
class Solution {
    public int singleNumber(int[] nums) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(nums[i])) {
                map.put(nums[i], map.get(nums[i]) + 1);
            } else {
                map.put(nums[i], 1);
            }
        }
        for (Integer key :
                map.keySet()) {
            if (map.get(key) == 1) {
                return key;
            }
        }
        return 0;
    }
}
```

```
解答成功:
执行耗时:5 ms,击败了27.49% 的Java用户
内存消耗:38.2 MB,击败了37.66% 的Java用户
```

按位计算是指每一位的和为3的倍数，取余可以计算出剩下的数字