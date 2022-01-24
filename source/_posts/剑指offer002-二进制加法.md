---
title: 剑指offer002-二进制加法
subtitle: 二进制加法
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer002-二进制加法

## 题目

<p>给定两个 01 字符串&nbsp;<code>a</code>&nbsp;和&nbsp;<code>b</code>&nbsp;，请计算它们的和，并以二进制字符串的形式输出。</p>

<p>输入为 <strong>非空 </strong>字符串且只包含数字&nbsp;<code>1</code>&nbsp;和&nbsp;<code>0</code>。</p>

<p>&nbsp;</p>

<p><strong>示例&nbsp;1:</strong></p>

<pre>
<strong>输入:</strong> a = &quot;11&quot;, b = &quot;10&quot;
<strong>输出:</strong> &quot;101&quot;</pre>

<p><strong>示例&nbsp;2:</strong></p>



<pre>
<strong>输入:</strong> a = &quot;1010&quot;, b = &quot;1011&quot;
<strong>输出:</strong> &quot;10101&quot;</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li>每个字符串仅由字符 <code>&#39;0&#39;</code> 或 <code>&#39;1&#39;</code> 组成。</li>
	<li><code>1 &lt;= a.length, b.length &lt;= 10^4</code></li>
	<li>字符串如果不是 <code>&quot;0&quot;</code> ，就都不含前导零。</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 67&nbsp;题相同：<a href="https://leetcode-cn.com/problems/add-binary/">https://leetcode-cn.com/problems/add-binary/</a></p>
<div><div>Related Topics</div><div><li>位运算</li><li>数学</li><li>字符串</li><li>模拟</li></div></div><br><div><li>👍 9</li><li>👎 0</li></div>

## 思路

### 思路一

一开始就考虑到数组的方法，比较直观比如输入`1011`和`110`

| 数组 | 0    | 1    | 2    | 3    | 4    |
| ---- | ---- | ---- | ---- | ---- | ---- |
| a    | 1    | 1    | 0    | 1    |      |
| b    | 0    | 1    | 1    |      |      |
| c    | 1    | 0    | 0    | 0    | 1    |

1. 考虑到有进位，因此数组大小应该是a，b数组大小的最大值+1
2. 数组a和数组b按照反方向入库，先低位再高位
3. 计算`c[i]`时，如果可以除了`c[i] = a[i] + b[i]`，可以考虑用`c[i]`来存储来自`i-1`的进位，所以`c[i] = a[i] + b[i] + c[i]`

先初始化所需要的信息

```java
int aSize = a.length();
int bSize = b.length();
int cSize = Math.max(aSize, bSize) + 1;
int[] aArr = new int[cSize];
int[] bArr = new int[cSize];
int[] cArr = new int[cSize];
```

为A，B赋值

```java
// 赋值
for (int i = aSize - 1; i >= 0; i--) {
    aArr[aSize - i - 1] = Integer.parseInt(String.valueOf(a.charAt(i)));
}

for (int i = bSize - 1; i >= 0; i--) {
    bArr[bSize - i - 1] = Integer.parseInt(String.valueOf(b.charAt(i)));
}
```

计算`c`，这里要考虑如果有进位，在`c[i+1]`中赋值这个进位

```java
// 计算
for (int i = 0; i < cArr.length - 1; i++) {
    int t = aArr[i] + bArr[i] + cArr[i];
    if (t < 2) {
        cArr[i] = t;
    } else {
        cArr[i] = t - 2;
        cArr[i + 1] = 1;
    }
}
```

反向输出字符串，忽略前面的0

```java
String res = "";
int flag = 0;
for (int i = cSize - 1; i >= 0; i--) {
    if(cArr[i] == 0 && flag == 0){
        flag = 1;
        continue;
    }
    flag = 1;
    res += cArr[i];
}
return res;
```

```
解答成功:
执行耗时:7 ms,击败了7.63% 的Java用户
内存消耗:38.4 MB,击败了36.93% 的Java用户
```

### 思路二

思路一是很正常的解题思路，完全模拟我们日常计算十进制加法，先对齐位数，按位加

思路一的运行速度很慢，内存消耗也很多，考虑到可能是数组比较大的时候，开辟三个空间比较耗时，因此优化的思路就是减少算法的空间复杂度

考虑直接从2个字符串的尾部开始加法，不额外开辟空间

```java
class Solution {
    public String addBinary(String a, String b) {
        int size = Math.max(a.length(), b.length());
        int step = 0;
        String res = "";
        for (int i = 0; i < size; i++) {
            int a_j = 0;
            int b_j = 0;
            if (a.length() - i - 1 < a.length() && a.length() - i - 1 >= 0) {
                a_j = Integer.parseInt(String.valueOf(a.charAt(a.length() - i - 1)));
            }

            if (b.length() - i - 1 < b.length() && b.length() - i - 1 >= 0) {
                b_j = Integer.parseInt(String.valueOf(b.charAt(b.length() - i - 1)));
            }


            int s = a_j + b_j + step;
            if (s < 2) {
                res += s;
                step = 0;
            } else {
                s = s - 2;
                res += s;
                step = 1;
            }
        }
        // 最后一个进位
        if (step == 1) {
            res += "1";
        }

        String res_new = "";
        for (int i = res.length() - 1; i >= 0; i--) {
            res_new += res.charAt(i);
        }
        return res_new;
    }
}
```

```
info
解答成功:
执行耗时:13 ms,击败了7.63% 的Java用户
内存消耗:38.5 MB,击败了14.50% 的Java用户
```

根据结果，执行耗时反而增加了。这边改变一下输出，结果是反向的，这边存的时候也反向追加

```java
class Solution {
    public String addBinary(String a, String b) {
        int size = Math.max(a.length(), b.length());
        int step = 0;
        String res = "";
        for (int i = 0; i < size; i++) {
            int a_j = 0;
            int b_j = 0;
            if (a.length() - i - 1 < a.length() && a.length() - i - 1 >= 0) {
                a_j = Integer.parseInt(String.valueOf(a.charAt(a.length() - i - 1)));
            }

            if (b.length() - i - 1 < b.length() && b.length() - i - 1 >= 0) {
                b_j = Integer.parseInt(String.valueOf(b.charAt(b.length() - i - 1)));
            }


            int s = a_j + b_j + step;
            if (s < 2) {
                res = s + res;
                step = 0;
            } else {
                s = s - 2;
                res = s + res;
                step = 1;
            }
        }
        if (step == 1) {
            res = "1" + res;
        }
        return res;
    }
}
```

```
解答成功:
执行耗时:10 ms,击败了7.63% 的Java用户
内存消耗:38.5 MB,击败了31.68% 的Java用户
```

还是没有提升，取消string的追加，以及字符的转化

```java
class Solution {
    public String addBinary(String a, String b) {
        int size = Math.max(a.length(), b.length());
        int step = 0;
        StringBuilder res=new StringBuilder();
        for (int i = 0; i < size; i++) {
            int a_j = 0;
            int b_j = 0;
            if (a.length() - i - 1 < a.length() && a.length() - i - 1 >= 0) {
                a_j = a.charAt(a.length() - i - 1) - '0';
            }

            if (b.length() - i - 1 < b.length() && b.length() - i - 1 >= 0) {
                b_j = b.charAt(b.length() - i - 1) - '0';
            }


            int s = a_j + b_j + step;
            if (s < 2) {
                res.append(s);
                step = 0;
            } else {
                s = s - 2;
                res.append(s);
                step = 1;
            }
        }
        if (step == 1) {
            res.append("1");
        }
        return res.reverse().toString();
    }
}
```

```
解答成功:
执行耗时:3 ms,击败了17.82% 的Java用户
内存消耗:38.4 MB,击败了56.32% 的Java用户
```



## 代码

### 思路一

```java
class Solution {
    public String addBinary(String a, String b) {
        int aSize = a.length();
        int bSize = b.length();
        int cSize = Math.max(aSize, bSize) + 1;
        int[] aArr = new int[cSize];
        int[] bArr = new int[cSize];
        int[] cArr = new int[cSize];

        // 赋值
        for (int i = aSize - 1; i >= 0; i--) {
            aArr[aSize - i - 1] = Integer.parseInt(String.valueOf(a.charAt(i)));
        }

        for (int i = bSize - 1; i >= 0; i--) {
            bArr[bSize - i - 1] = Integer.parseInt(String.valueOf(b.charAt(i)));
        }

        // 计算
        for (int i = 0; i < cArr.length - 1; i++) {
            int t = aArr[i] + bArr[i] + cArr[i];
            if (t < 2) {
                cArr[i] = t;
            } else {
                cArr[i] = t - 2;
                cArr[i + 1] = 1;
            }
        }
        String res = "";
        int flag = 0;
        for (int i = cSize - 1; i >= 0; i--) {
            if(cArr[i] == 0 && flag == 0){
                flag = 1;
                continue;
            }
            flag = 1;
            res += cArr[i];
        }
        return res;
    }
}
```



