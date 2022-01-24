---
title: 剑指offer019-最多删除一个字符得到回文
subtitle: 最多删除一个字符得到回文
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer019-最多删除一个字符得到回文

## 题目

<p>给定一个非空字符串&nbsp;<code>s</code>，请判断如果&nbsp;<strong>最多 </strong>从字符串中删除一个字符能否得到一个回文字符串。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入:</strong> s = &quot;aba&quot;
<strong>输出:</strong> true
</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong> s = &quot;abca&quot;
<strong>输出:</strong> true
<strong>解释:</strong> 可以删除 &quot;c&quot; 字符 或者 &quot;b&quot; 字符
</pre>

<p><strong>示例 3:</strong></p>

<pre>
<strong>输入:</strong> s = &quot;abc&quot;
<strong>输出:</strong> false</pre>

<p>&nbsp;</p>

<p><strong>提示:</strong></p>

<ul>
	<li><code>1 &lt;= s.length &lt;= 10<sup>5</sup></code></li>
	<li><code>s</code> 由小写英文字母组成</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 680&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/valid-palindrome-ii/">https://leetcode-cn.com/problems/valid-palindrome-ii/</a></p>
<div><div>Related Topics</div><div><li>贪心</li><li>双指针</li><li>字符串</li></div></div><br><div><li>👍 13</li><li>👎 0</li></div>

## 思路

首先就是如果这个字符串本身就是回文，就不需要改动

先实现一个函数，用于判断字符串是否是回文，这个方法有很多，左右指针等，但是这里采用了代码编写上最简单的

```java
private boolean isHui(String s) {
    StringBuilder sb = new StringBuilder(s);
    if (sb.toString().equals(sb.reverse().toString())) {
        return true;
    }
    return false;
}
```

```java
if (isHui(s)) {
    return true;
}
```

如果`s`不是回文，那么就一定会删除一个字符

这个字符一开始我想着从中心往外面，碰到不一致的，就删除左边或者右边，发现这样不行，因为删除一个字符会导致回文的中心变动

所以应该从两边往中心走，如果`left==right`，说明是没有问题的，一旦`left！=right`，这时候就需要考虑删除左边的还是右边的。当删除左或者删除右有一个能成为回文，则是回文，否则不是

```java
int right = n - 1;
for (int i = 0; i < n / 2; i++) {
    if (s.charAt(i) == s.charAt(right)) {
        right -= 1;
        continue;
    } else {
        // 删除left
        // 或者删除right
        return isHui(s.substring(i + 1, right + 1)) || isHui(s.substring(i, right));
    }
}
```

最后，当`s`的长度`n`是`1，2`时，必定是回文

```java
int n = s.length();
if (n == 1 || n == 2) {
    return true;
}
```



## 代码

```java
class Solution {
    public boolean validPalindrome(String s) {
        int n = s.length();
        if (n == 1 || n == 2) {
            return true;
        }

        if (isHui(s)) {
            return true;
        }

        int right = n - 1;
        for (int i = 0; i < n / 2; i++) {
            if (s.charAt(i) == s.charAt(right)) {
                right -= 1;
                continue;
            } else {
                // 删除left
                // 或者删除right
                return isHui(s.substring(i + 1, right + 1)) || isHui(s.substring(i, right));
            }
        }
        return true;
    }

    private boolean isHui(String s) {
        StringBuilder sb = new StringBuilder(s);
        if (sb.toString().equals(sb.reverse().toString())) {
            return true;
        }
        return false;
    }
}
```

```
解答成功:
执行耗时:9 ms,击败了12.05% 的Java用户
内存消耗:39.1 MB,击败了10.47% 的Java用户
```

优化一下回文的判断逻辑

```java
private boolean isHui(String s) {
    int n = s.length();
    for (int i = 0; i < n / 2; i++) {
        if (s.charAt(i) != s.charAt(n - i - 1)) {
            return false;
        }
    }
    return true;
}
```

```
解答成功:
执行耗时:7 ms,击败了42.86% 的Java用户
内存消耗:38.9 MB,击败了47.98% 的Java用户
```





