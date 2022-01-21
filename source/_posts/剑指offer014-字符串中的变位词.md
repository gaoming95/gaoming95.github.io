---
title: 剑指offer014-字符串中的变位词
subtitle: 符串中的变位词
date: 2021-11-03
author: 高明
tags:
	- 剑指offer
---



# 剑指offer014-字符串中的变位词

## 题目

<p>给定两个字符串&nbsp;<code>s1</code>&nbsp;和&nbsp;<code>s2</code>，写一个函数来判断 <code>s2</code> 是否包含 <code>s1</code><strong>&nbsp;</strong>的某个变位词。</p>

<p>换句话说，第一个字符串的排列之一是第二个字符串的 <strong>子串</strong> 。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入: </strong>s1 = &quot;ab&quot; s2 = &quot;eidbaooo&quot;
<strong>输出: </strong>True
<strong>解释:</strong> s2 包含 s1 的排列之一 (&quot;ba&quot;).
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入: </strong>s1= &quot;ab&quot; s2 = &quot;eidboaoo&quot;
<strong>输出:</strong> False
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= s1.length, s2.length &lt;= 10<sup>4</sup></code></li>
	<li><code>s1</code> 和 <code>s2</code> 仅包含小写字母</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 567&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/permutation-in-string/">https://leetcode-cn.com/problems/permutation-in-string/</a></p>
<div><div>Related Topics</div><div><li>哈希表</li><li>双指针</li><li>字符串</li><li>滑动窗口</li></div></div><br><div><li>👍 19</li><li>👎 0</li></div>

## 思路

滑动窗口，记录窗口内的字串

## 代码

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        int left = 0;
        int right = 0;
        Map<Character, Integer> s1Map = new HashMap<>();
        for (int i = 0; i < s1.length(); i++) {
            char c = s1.charAt(i);
            s1Map.put(c, s1Map.getOrDefault(c, 0) + 1);
        }

        Map<Character, Integer> s2Map = new HashMap<>();
        s2Map.put(s2.charAt(0), 1);
        while (right - left + 1 <= s2.length() && right < s2.length()) {
            if (!s1Map.containsKey(s2.charAt(right))) {
                left = right = right + 1;
                if (right >= s2.length()) {
                    return false;
                }
                s2Map.clear();
                s2Map.put(s2.charAt(right), s2Map.getOrDefault(s2.charAt(right), 0) + 1);
            } else if (right - left + 1 < s1.length()) {
                right += 1;
                if (right >= s2.length()) {
                    return false;
                }
                s2Map.put(s2.charAt(right), s2Map.getOrDefault(s2.charAt(right), 0) + 1);
            } else if (right - left + 1 == s1.length()) {
                if (!judge(s1Map, s2Map)) {
                    right += 1;
                    left += 1;
                    if (right >= s2.length()) {
                        return false;
                    }
                    s2Map.put(s2.charAt(right), s2Map.getOrDefault(s2.charAt(right), 0) + 1);
                    s2Map.put(s2.charAt(left - 1), s2Map.getOrDefault(s2.charAt(left - 1), 0) - 1);
                } else {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean judge(Map<Character, Integer> s1Map, Map<Character, Integer> s2Map) {
        if (s2Map.keySet().size() != s1Map.keySet().size()) {
            return false;
        }

        for (Character key :
                s2Map.keySet()) {
            if (!s1Map.containsKey(key)) {
                return false;
            } else {
                if (!s1Map.get(key).equals(s2Map.get(key))) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

```
解答成功:
执行耗时:25 ms,击败了16.09% 的Java用户
内存消耗:39 MB,击败了5.41% 的Java用户
```

