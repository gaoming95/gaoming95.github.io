---
title: 剑指offer018-有效的回文
subtitle: 有效的回文
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer018-有效的回文

## 题目

<p>给定一个字符串 <code>s</code> ，验证 <code>s</code>&nbsp;是否是&nbsp;<strong>回文串&nbsp;</strong>，只考虑字母和数字字符，可以忽略字母的大小写。</p>

<p>本题中，将空字符串定义为有效的&nbsp;<strong>回文串&nbsp;</strong>。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入: </strong>s =<strong> </strong>&quot;A man, a plan, a canal: Panama&quot;
<strong>输出:</strong> true
<strong>解释：</strong>&quot;amanaplanacanalpanama&quot; 是回文串</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong> s = &quot;race a car&quot;
<strong>输出:</strong> false
解释：&quot;raceacar&quot; 不是回文串</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= s.length &lt;= 2 * 10<sup>5</sup></code></li>
	<li>字符串 <code>s</code> 由 ASCII 字符组成</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 125&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/valid-palindrome/">https://leetcode-cn.com/problems/valid-palindrome/</a></p>
<div><div>Related Topics</div><div><li>双指针</li><li>字符串</li></div></div><br><div><li>👍 9</li><li>👎 0</li></div>

## 思路

一开始的想法肯定是先去掉非法的字符，全部转化为小写，判断该字符串左右相等

```java
class Solution {
    public boolean isPalindrome(String s) {
        s = s.toLowerCase();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if ((Character.isLetterOrDigit(s.charAt(i)))) {
                sb.append(s.charAt(i));
            }
        }
        return sb.toString().equals(sb.reverse().toString());
    }
}
```

这里要注意数字和字符的判断

## 代码

```java
class Solution {
    public boolean isPalindrome(String s) {
        s = s.toLowerCase();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if ((Character.isLetterOrDigit(s.charAt(i)))) {
                sb.append(s.charAt(i));
            }
        }
        return sb.toString().equals(sb.reverse().toString());
    }
}
```

```
解答成功:
执行耗时:5 ms,击败了17.77% 的Java用户
内存消耗:38 MB,击败了96.98% 的Java用户
```



```java
class Solution {
    int offset = 'a' - 'A';
    public boolean isPalindrome(String s) {
        int n = s.length();
        int left = n - 1;
        for (int i = 0; i < s.length(); i++) {
            if (!Character.isLetterOrDigit(s.charAt(i))) {
                continue;
            }
            while (!Character.isLetterOrDigit(s.charAt(left)) && left > 0) {
                left--;
            }
            if (convert(s.charAt(i)) != convert(s.charAt(left))) {
                return false;
            }
            left--;
        }
        return true;
    }

    public char convert(char c) {
        if (c >= 'A' && c <= 'Z') {
            return (char) (c + offset);
        }
        return c;
    }
}
```

```
解答成功:
执行耗时:3 ms,击败了60.37% 的Java用户
内存消耗:38.5 MB,击败了45.08% 的Java用户
```



