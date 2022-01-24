---
title: 剑指offer035-最小时间差
subtitle: 最小时间差
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer035-最小时间差

## 题目

<p>给定一个 24 小时制（小时:分钟 <strong>&quot;HH:MM&quot;</strong>）的时间列表，找出列表中任意两个时间的最小时间差并以分钟数表示。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>timePoints = [&quot;23:59&quot;,&quot;00:00&quot;]
<strong>输出：</strong>1
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>timePoints = [&quot;00:00&quot;,&quot;23:59&quot;,&quot;00:00&quot;]
<strong>输出：</strong>0
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>2 &lt;= timePoints &lt;= 2 * 10<sup>4</sup></code></li>
	<li><code>timePoints[i]</code> 格式为 <strong>&quot;HH:MM&quot;</strong></li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 539&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/minimum-time-difference/">https://leetcode-cn.com/problems/minimum-time-difference/</a></p>
<div><div>Related Topics</div><div><li>数组</li><li>数学</li><li>字符串</li><li>排序</li></div></div><br><div><li>👍 6</li><li>👎 0</li></div>

## 思路

想到还是很好想的

比如两个时间点 A, B

0------|-------|-------24
        A        B

因为是环形的，所以A和B的最小值要么是 B-A，要么是 24-B + A-0

所以我们可以构建数组，以分钟为值

```java
int m = timePoints.size();
int[] arr = new int[m];

for (int i = 0; i < m; i++) {
    String t = timePoints.get(i);
    String[] ts = t.split(":");
    int h1 = Integer.valueOf(ts[0]);
    int m1 = Integer.valueOf(ts[1]);
    int hm = h1 * 60 + m1;
    arr[i] = hm;
}
```

0------|--------|--------|----------|----------|---------24

对于数组中的数据，如果遍历，就是取任意两个数据 计算 B-A和 24-B + A-0的最小值

但是如果先排序，则B-A的最小值只会是相邻之间产生

 24-B + A-0取最小值，则B是最大值，A要是最小值，即首位

```java
Arrays.sort(arr);
int res = Integer.MAX_VALUE;
for (int i = 0; i < m - 1; i++) {
    res = Math.min(arr[i + 1] - arr[i], res);
}
res = Math.min(24 * 60 - arr[m - 1] + arr[0], res);
return res;
```

## 代码

```java
class Solution {
    public int findMinDifference(List<String> timePoints) {
        if (timePoints.size() < 2) {
            return 0;
        }
        int m = timePoints.size();
        int[] arr = new int[m];

        for (int i = 0; i < m; i++) {
            String t = timePoints.get(i);
            String[] ts = t.split(":");
            int h1 = Integer.valueOf(ts[0]);
            int m1 = Integer.valueOf(ts[1]);
            int hm = h1 * 60 + m1;
            arr[i] = hm;
        }

        Arrays.sort(arr);

        int res = Integer.MAX_VALUE;

        for (int i = 0; i < m - 1; i++) {
            res = Math.min(arr[i + 1] - arr[i], res);
        }
        res = Math.min(24 * 60 - arr[m - 1] + arr[0], res);
        return res;
    }
}
```

