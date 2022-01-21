---
title: 剑指offer010-和为k的子数组
subtitle: 和为k的子数组
date: 2021-11-03
author: 高明
tags:
	- 剑指offer
---



# 剑指offer010-和为k的子数组

## 题目
<p>给定一个整数数组和一个整数&nbsp;<code>k</code><strong> ，</strong>请找到该数组中和为&nbsp;<code>k</code><strong>&nbsp;</strong>的连续子数组的个数。</p>

<p>&nbsp;</p>

<p><strong>示例 1 :</strong></p>

<pre>
<strong>输入:</strong>nums = [1,1,1], k = 2
<strong>输出:</strong> 2
<strong>解释:</strong> 此题 [1,1] 与 [1,1] 为两种不同的情况
</pre>

<p><strong>示例 2&nbsp;:</strong></p>

<pre>
<strong>输入:</strong>nums = [1,2,3], k = 3
<strong>输出:</strong> 2
</pre>

<p>&nbsp;</p>

<p><strong>提示:</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 2 * 10<sup>4</sup></code></li>
	<li><code>-1000 &lt;= nums[i] &lt;= 1000</code></li>
	<li>
	<p><code>-10<sup>7</sup>&nbsp;&lt;= k &lt;= 10<sup>7</sup></code></p>
	</li>
</ul>

<p>&nbsp;</p>

<p>注意：本题与主站 560&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/subarray-sum-equals-k/">https://leetcode-cn.com/problems/subarray-sum-equals-k/</a></p>
<div><div>Related Topics</div><div><li>数组</li><li>哈希表</li><li>前缀和</li></div></div><br><div><li>👍 23</li><li>👎 0</li></div>

## 思路

一开始想到的就是滑动窗口

三层for循环

```java
for i in 0,len
	for j in i,len
		sum(i,j)
```

这边`sum(i,j)`的时候，需要从`k`从`i~j`

三层`for`循环肯定过不了，考虑前缀和来记录`0-i`的和

即`ai = nums[0] + nums[1] + ... + nums[i]`

这样`sum(i, j) = a[j] - a[i] + nums[i]`

```java
int[] arr = new int[nums.length];
arr[0] = nums[0];
for (int i = 1; i < arr.length; i++) {
    arr[i] = arr[i - 1] + nums[i];
}
```

```java
int res = 0;
for (int i = 0; i < nums.length; i++) {
    for (int j = i; j < nums.length; j++) {
        int s = arr[j] - arr[i] + nums[i];
        if (s == k) {
            res += 1;
        }
    }
}
return res;
```

当然，前缀和这个用在这里优点麻烦了，可以直接用`sum`进行局部求和

看了题解，发现问题可以变得更简单，如图

比如`[1,2,1,2,1]` 和为`3`

我们记录数组`sum[i]`为`0~i-1`的和

`sum[0] = 0`

| 0    | 1    | 3    | 4    | 6    | 7    |
| ---- | ---- | ---- | ---- | ---- | ---- |

所以整个问题就转化为从`sum`数组中找到两个数字，满足`a-b=target` (a > b)

即找到一个数`b = a - target`

这个可以参考两数之和，变成了两数之差

```java
Map<Integer, Integer> map = new HashMap<>();
for (int i = 1; i < sum.length; i++) {
    if (map.containsKey(sum[i] - k)) { // sum[i] -k = b (判断b在不在map里面)
        // 里面存在目标值
    }
    // 不管包不包含，将sum[i] = a 记录到map中，如果存在就+1，不存在设置为1
    map.put(sum[i], map.getOrDefault(sum[i], 0) + 1);
}
```

所以`map`中实际上存的是`sum`数组中，每一项以及每一项出现的次数即`{0:1, 1:1, 3: 1, 4:1, 6:1, 7:1}`

如果遍历到某一项时，判断`a-target=b`在里面，说明找到了符合条件的`a`和`b`，满足`a-b=target`

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int[] sum = new int[nums.length + 1];
        sum[0] = 0;
        for (int i = 1; i < sum.length; i++) {
            sum[i] = sum[i - 1] + nums[i - 1];
        }
        Map<Integer, Integer> map = new HashMap<>();
        int count = 0;
        for (int i = 0; i < sum.length; i++) {
            if (map.containsKey(sum[i] - k)) {
                count = count + map.get(sum[i] - k);
            }
            map.put(sum[i], map.getOrDefault(sum[i], 0) + 1);
        }
        return count;
    }
}
```

上述代码可以再优化一下空间复杂度，目前我们保存了这个`sum`数组，实际上这个`sum`数组是由`arr`数组累加得到的，我们可以再运行过程中处理这个，把sum数组的计算去掉，这样我们需要增加`map.put(0, 1);`和上面的数组保持一致

```java
class Solution {
    public int subarraySum(int[] nums, int k) {

        int sum = 0;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            if (map.containsKey(sum - k)) {
                count = count + map.get(sum - k);
            }
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return count;
    }
}
```

```
解答成功:
执行耗时:22 ms,击败了85.14% 的Java用户
内存消耗:40.8 MB,击败了79.07% 的Java用户
```



## 代码

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int[] arr = new int[nums.length];
        arr[0] = nums[0];
        for (int i = 1; i < arr.length; i++) {
            arr[i] = arr[i - 1] + nums[i];
        }

        int res = 0;
        for (int i = 0; i < nums.length; i++) {
            for (int j = i; j < nums.length; j++) {
                int s = arr[j] - arr[i] + nums[i];
                if (s == k) {
                    res += 1;
                }
            }
        }
        return res;
    }
}
```

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int[] sum = new int[nums.length + 1];
        sum[0] = 0;
        for (int i = 1; i < sum.length; i++) {
            sum[i] = sum[i - 1] + nums[i - 1];
        }


        Map<Integer, Integer> map = new HashMap<>();
        int count = 0;
        for (int i = 0; i < sum.length; i++) {
            if (map.containsKey(sum[i] - k)) {
                count = count + map.get(sum[i] - k);
            }
            map.put(sum[i], map.getOrDefault(sum[i], 0) + 1);
        }
        return count;
    }
}
```

```
解答成功:
执行耗时:17 ms,击败了96.90% 的Java用户
内存消耗:41.4 MB,击败了54.81% 的Java用户
```

```java
class Solution {
    public int subarraySum(int[] nums, int k) {

        int sum = 0;
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, 1);
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            if (map.containsKey(sum - k)) {
                count = count + map.get(sum - k);
            }
            map.put(sum, map.getOrDefault(sum, 0) + 1);
        }
        return count;
    }
}
```

```
解答成功:
执行耗时:22 ms,击败了85.14% 的Java用户
内存消耗:40.8 MB,击败了79.07% 的Java用户
```

