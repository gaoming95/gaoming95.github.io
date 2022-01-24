---
title: 剑指offer016-不含重复字符的最长子字符串
subtitle: 不含重复字符的最长子字符串
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer016-不含重复字符的最长子字符串

## 题目

<p>给定一个字符串 <code>s</code> ，请你找出其中不含有重复字符的&nbsp;<strong>最长连续子字符串&nbsp;</strong>的长度。</p>

<p>&nbsp;</p>

<p><strong>示例&nbsp;1:</strong></p>

<pre>
<strong>输入: </strong>s = &quot;abcabcbb&quot;
<strong>输出: </strong>3 
<strong>解释:</strong> 因为无重复字符的最长子字符串是 <code>&quot;abc&quot;，所以其</code>长度为 3。
</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入: </strong>s = &quot;bbbbb&quot;
<strong>输出: </strong>1
<strong>解释: </strong>因为无重复字符的最长子字符串是 <code>&quot;b&quot;</code>，所以其长度为 1。
</pre>

<p><strong>示例 3:</strong></p>

<pre>
<strong>输入: </strong>s = &quot;pwwkew&quot;
<strong>输出: </strong>3
<strong>解释: </strong>因为无重复字符的最长子串是&nbsp;<code>&quot;wke&quot;</code>，所以其长度为 3。
&nbsp;    请注意，你的答案必须是 <strong>子串 </strong>的长度，<code>&quot;pwke&quot;</code>&nbsp;是一个<em>子序列，</em>不是子串。
</pre>

<p><strong>示例 4:</strong></p>

<pre>
<strong>输入: </strong>s = &quot;&quot;
<strong>输出: </strong>0
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>0 &lt;= s.length &lt;= 5 * 10<sup>4</sup></code></li>
	<li><code>s</code>&nbsp;由英文字母、数字、符号和空格组成</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 3&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/">https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/</a></p>
<div><div>Related Topics</div><div><li>哈希表</li><li>字符串</li><li>滑动窗口</li></div></div><br><div><li>👍 13</li><li>👎 0</li></div>

## 思路

一开始想到的就是滑动窗口，或者双指针。

| a    | b    | c    | a    | b    | c    | b    | b    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| i    |      |      | j    |      |      |      |      |

双指针用起来很麻烦，建议用到双指针的时候，尽量用一层for循环，对i进行遍历

如果定义两个`left`和`right`，`left`和`right`的下标控制起来很麻烦，容易出现问题

一开始的想法当`j`指向第二个`a`的时候，`i`应该指向下一个元素`b`，此时从`a-b`内的元素应该从`map`中清除

## 代码

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        Map<Integer, Integer> map = new HashMap();
        int left = 0;
        int res = 0;
        for(int i = 0; i < n;i ++){
            Integer key = s.charAt(i) - 'a';
            if(map.containsKey(key)){
                int index = map.get(key);
                for(int j = left; j < index+1; j++){
                    map.remove(s.charAt(j)-'a');
                }
                left = index + 1;
            }
            map.put(key, i);
            res = Math.max(res, i - left + 1);
        }
        return res;
    }
}
```

