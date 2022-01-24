---
title: 剑指offer005-单词长度的最大乘积
subtitle: 单词长度的最大乘积
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer005-单词长度的最大乘积

## 题目

<p>给定一个字符串数组&nbsp;<code>words</code>，请计算当两个字符串 <code>words[i]</code> 和 <code>words[j]</code> 不包含相同字符时，它们长度的乘积的最大值。假设字符串中只包含英语的小写字母。如果没有不包含相同字符的一对字符串，返回 0。</p>

<p>&nbsp;</p>

<p><strong>示例&nbsp;1:</strong></p>

<pre>
<strong>输入:</strong> words = <code>[&quot;abcw&quot;,&quot;baz&quot;,&quot;foo&quot;,&quot;bar&quot;,&quot;fxyz&quot;,&quot;abcdef&quot;]</code>
<strong>输出: </strong><code>16 
<strong>解释:</strong> 这两个单词为<strong> </strong></code><code>&quot;abcw&quot;, &quot;fxyz&quot;</code>。它们不包含相同字符，且长度的乘积最大。</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong> words = <code>[&quot;a&quot;,&quot;ab&quot;,&quot;abc&quot;,&quot;d&quot;,&quot;cd&quot;,&quot;bcd&quot;,&quot;abcd&quot;]</code>
<strong>输出: </strong><code>4 
<strong>解释: </strong></code>这两个单词为 <code>&quot;ab&quot;, &quot;cd&quot;</code>。</pre>

<p><strong>示例 3:</strong></p>

<pre>
<strong>输入:</strong> words = <code>[&quot;a&quot;,&quot;aa&quot;,&quot;aaa&quot;,&quot;aaaa&quot;]</code>
<strong>输出: </strong><code>0 
<strong>解释: </strong>不存在这样的两个单词。</code>
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>2 &lt;= words.length &lt;= 1000</code></li>
	<li><code>1 &lt;= words[i].length &lt;= 1000</code></li>
	<li><code>words[i]</code>&nbsp;仅包含小写字母</li>
</ul>
## 思路

没啥思路，就是穷举所有的可能性，在比较2个单词是否有重复单词的时候，可以用HashSet，把o(n^2)变为两个o(n)

```java
class Solution {
    public int maxProduct(String[] words) {
        int res = 0;
        for (int i = 0; i < words.length; i++) {
            for (int j = i + 1; j < words.length; j++) {
                if (!isHasSame(words[i], words[j])) {
                    int size = words[i].length() * words[j].length();
                    res = Math.max(res, size);
                }
            }
        }
        return res;
    }

    private boolean isHasSame(String w1, String w2) {
        HashSet<Character> characters = new HashSet<>();

        for (int i = 0; i < w1.length(); i++) {
            characters.add(w1.charAt(i));
        }

        for (int i = 0; i < w2.length(); i++) {
            if (characters.contains(w2.charAt(i))) {
                return true;
            }
        }
        return false;
    }
}
```

