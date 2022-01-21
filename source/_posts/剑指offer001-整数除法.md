---
title: 剑指offer001-整数除法
subtitle: 整数除法
date: 2021-11-01
author: 高明
tags:
	- 剑指offer
---

# 剑指offer001-整数除法

## 题目


<p>给定两个整数 <code>a</code> 和 <code>b</code> ，求它们的除法的商 <code>a/b</code> ，要求不得使用乘号 <code>&#39;*&#39;</code>、除号 <code>&#39;/&#39;</code> 以及求余符号 <code>&#39;%&#39;</code>&nbsp;。</p>
<p>&nbsp;</p>

<p><strong>注意：</strong></p>

<ul>
	<li>整数除法的结果应当截去（<code>truncate</code>）其小数部分，例如：<code>truncate(8.345) = 8</code>&nbsp;以及&nbsp;<code>truncate(-2.7335) = -2</code></li>
	<li>假设我们的环境只能存储 32 位有符号整数，其数值范围是 <code>[&minus;2<sup>31</sup>,&nbsp;2<sup>31</sup>&minus;1]</code>。本题中，如果除法结果溢出，则返回 <code>2<sup>31&nbsp;</sup>&minus; 1</code></li>
</ul>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>a = 15, b = 2
<strong>输出：</strong>7
<strong><span style="white-space: pre-wrap;">解释：</span></strong>15/2 = truncate(7.5) = 7
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>a = 7, b = -3
<strong>输出：</strong><span style="white-space: pre-wrap;">-2</span>
<strong><span style="white-space: pre-wrap;">解释：</span></strong>7/-3 = truncate(-2.33333..) = -2</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>a = 0, b = 1
<strong>输出：</strong><span style="white-space: pre-wrap;">0</span></pre>

<p><strong>示例 4：</strong></p>

<pre>
<strong>输入：</strong>a = 1, b = 1
<strong>输出：</strong><span style="white-space: pre-wrap;">1</span></pre>

<p>&nbsp;</p>

<p><strong>提示:</strong></p>

<ul>
	<li><code>-2<sup>31</sup>&nbsp;&lt;= a, b &lt;= 2<sup>31</sup>&nbsp;- 1</code></li>
	<li><code>b != 0</code></li>
</ul>

<p>&nbsp;</p>

<p>注意：本题与主站 29&nbsp;题相同：<a href="https://leetcode-cn.com/problems/divide-two-integers/">https://leetcode-cn.com/problems/divide-two-integers/</a></p>

<p>&nbsp;</p>
<div><div>Related Topics</div><div><li>位运算</li><li>数学</li></div></div><br><div><li>👍 51</li><li>👎 0</li></div>

## 思路

最初的想法是基于二分查找，即`a/b=c`，我们已知`a,b` 求`c`

即`b*c=a`，`c`是未知的，我们可以从`1-a`中二分查找，我们可以统一正负数，用正数去处理

```java
int left = 1;
int right = a;
while (left < right) {
    long mid = left + ((right - left) / 2); // 这边避免溢出
    int t = mid * b;
    if (t > a) {
        right = mid - 1;
    } else if (t < a) {
        left = mid + 1;
    } else {
        return mid;
    }
    return left;
}
```

很显然，乘法是不能存在的

所以，这边优化乘法计算，快速乘法

比如：`5*53`，可以将`53`作为二进制`110101`，最后计算只要计算`5*(100000 + 10000 + 100 + 1)`，这里不是十进制的乘法，而是二进制，即对5这个数字，5*（32 + 16 + 4 + 1），只要左移固定的位数就好

```java
private int fastAdd(int x, int y) {
    int ans = 0;
    int step = 0;
    while (y > 0) {
        int l = y & 1; // 记录最后一位
        if (l == 1) { // 只有最后一位是1的时候
            int t = x << step; // 左移 step
            ans += t; // 累加
        }
        step += 1;
        y = y >> 1; // y 右移一位
    }
    return ans;
}
```

所以整体上，只要替换上面的乘法就好了，即`int t = fastAdd(mid, b);`

但是出现了一个问题，就是在计算`int t = fastAdd(mid, b);`的时候`t`会溢出，在整个二分法的过程中，其实我们不关心`mid * b`的大小，我们想知道它与`a`的大小关系

即我们关心`mid * b - a > 0 ?`

所以改造`fastAdd`方法

```java
private int fastAdd(int x, int y, int a) {

    int ans = 0;
    int step = 0;
    while (y > 0) {
        int l = y & 1;
        if (l == 1) {
            int t = x << step;
            ans = ans + t;
        }
        step = step + 1;
        y = y >> 1;
        if (ans > a) {
            return 1;
        }
    }
    int delta = ans - a;
    if (delta > 0) {
        return 1;
    } else if (delta < 0) {
        return -1;
    } else {
        return 0;
    }
}
```

因此上述二分法可以改造为

```java
int left = 1;
int right = a;
while (left <= right) {
    int mid = left + ((right - left) >> 1);
    int t = fastAdd(mid, b, a);
    if (t > 0) {
        right = mid - 1;
    } else if (t < 0) {
        left = mid + 1;
    } else {
        return mid;
    }
}
return left;
```

结合符号判断等，完成代码

```java
class Solution {
    public int divide(int a, int b) {
        if (a == 0) {
            return 0;
        }
        if (b == 1) {
            return a;
        }
        if (a == Integer.MIN_VALUE && b == -1) {
            return Integer.MAX_VALUE;
        }
        int flag = 1;
        if (a < 0) {
            a = -a;
            flag = -flag;
        }
        if (b < 0) {
            b = -b;
            flag = -flag;
        }
        if (a < b) {
            return 0;
        }
        if (a == b) {
            return flag;
        }
        int left = 1;
        int right = a;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            int t = fastAdd(mid, b, a);
            if (t > 0) {
                right = mid - 1;
            } else if (t < 0) {
                left = mid + 1;
            } else {
                return mid;
            }
        }
        return flag > 0 ? Math.min(left, right) : -Math.min(left, right);
    }
    private int fastAdd(int x, int y, int a) {

        int ans = 0;
        int step = 0;
        while (y > 0) {
            int l = y & 1;
            if (l == 1) {
                int t = x << step;
                ans = ans + t;
            }
            step = step + 1;
            y = y >> 1;
            if (ans > a) {
                return 1;
            }
        }
        int delta = ans - a;
        if (delta > 0) {
            return 1;
        } else if (delta < 0) {
            return -1;
        } else {
            return 0;
        }
    }
}
```

这边还是出现了问题，就是一开始输入的时候，系统输入`-2147483648`，我们一开始就直接转化为`2147483648`，直接溢出，导致错误。这边查看了其他人的方法，大部分是转为long计算，还有一开始将正数转为负数，直接用负数进行计算

这边觉得转为long可以避免这种情况，用正数做逻辑上比较清晰

## 代码

```java
class Solution {
    public int divide(int x, int y) {

        long a = (long) x;
        long b = (long) y;
        if (a == 0) {
            return 0;
        }
        if (b == 1) {
            return (int) a;
        }

        if (a == Integer.MIN_VALUE && b == -1) {
            return Integer.MAX_VALUE;
        }

        int flag = 1;
        if (a < 0) {
            a = -a;
            flag = -flag;
        }
        if (b < 0) {
            b = -b;
            flag = -flag;
        }
        if (a < b) {
            return 0;
        }
        if (a == b) {
            return flag;
        }

        long left = 1;
        long right = a;
        while (left <= right) {
            long mid = left + ((right - left) >> 1);
            int t = fastAdd(mid, b, a);
            if (t > 0) {
                right = mid - 1;
            } else if (t < 0) {
                left = mid + 1;
            } else {
                return flag > 0 ? (int) mid : (int) -mid;
            }
        }

        return flag > 0 ? (int) Math.min(left, right) : (int) -Math.min(left, right);
    }
    private int fastAdd(long x, long y, long a) {

        long ans = 0;
        long step = 0;
        while (y > 0) {
            long l = y & 1;
            if (l == 1) {
                long t = x << step;
                ans = ans + t;
            }
            step = step + 1;
            y = y >> 1;
            if (ans > a) {
                return 1;
            }
        }
        int delta = (int) (ans - a);
        if (delta > 0) {
            return 1;
        } else if (delta < 0) {
            return -1;
        } else {
            return 0;
        }
    }
}
```

```
解答成功:
执行耗时:1 ms,击败了97.85% 的Java用户
内存消耗:35.5 MB,击败了61.03% 的Java用户
```

