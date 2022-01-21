---
title: 剑指offer017-含有所有字符的最短字符串
subtitle: 含有所有字符的最短字符串
date: 2021-11-03
author: 高明
tags:
	- 剑指offer
---



# 剑指offer017-含有所有字符的最短字符串

## 题目

<p>给定两个字符串 <code>s</code> 和&nbsp;<code>t</code> 。返回 <code>s</code> 中包含&nbsp;<code>t</code>&nbsp;的所有字符的最短子字符串。如果 <code>s</code> 中不存在符合条件的子字符串，则返回空字符串 <code>&quot;&quot;</code> 。</p>

<p>如果 <code>s</code> 中存在多个符合条件的子字符串，返回任意一个。</p>

<p>&nbsp;</p>

<p><strong>注意： </strong>对于 <code>t</code> 中重复字符，我们寻找的子字符串中该字符数量必须不少于 <code>t</code> 中该字符数量。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>s = &quot;ADOBECODEBANC&quot;, t = &quot;ABC&quot;
<strong>输出：</strong>&quot;BANC&quot; 
<strong>解释：</strong>最短子字符串 &quot;BANC&quot; 包含了字符串 t 的所有字符 &#39;A&#39;、&#39;B&#39;、&#39;C&#39;</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>s = &quot;a&quot;, t = &quot;a&quot;
<strong>输出：</strong>&quot;a&quot;
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>s = &quot;a&quot;, t = &quot;aa&quot;
<strong>输出：</strong>&quot;&quot;
<strong>解释：</strong>t 中两个字符 &#39;a&#39; 均应包含在 s 的子串中，因此没有符合条件的子字符串，返回空字符串。</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= s.length, t.length &lt;= 10<sup>5</sup></code></li>
	<li><code>s</code> 和 <code>t</code> 由英文字母组成</li>
</ul>

<p>&nbsp;</p>

<p><strong>进阶：</strong>你能设计一个在 <code>o(n)</code> 时间内解决此问题的算法吗？</p>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 76&nbsp;题相似（本题答案不唯一）：<a href="https://leetcode-cn.com/problems/minimum-window-substring/">https://leetcode-cn.com/problems/minimum-window-substring/</a></p>
<div><div>Related Topics</div><div><li>哈希表</li><li>字符串</li><li>滑动窗口</li></div></div><br><div><li>👍 12</li><li>👎 0</li></div>

## 思路

### 错误想法1

一开始想着记录所有的字符以及他们下标位置，这样就退化成了，字符串变位词问题

比如 `ADOBECODEBANC`变成`ABCBAC`，然后找到`ABC`的变位词，这样的想法是错误的，因为变位词的要求是长度一致，但是这个长度是可以不一致的

后面想到还是用滑动窗口，固定`left`从`0`开始，`right`从`i`到`n`

一开始，先记录一下`t`字符串所需要的数据`map`

```java
Map<Character, Integer> tMap = new HashMap<>();

for (int i = 0; i < tLen; i++) {
    tMap.put(t.charAt(i), tMap.getOrDefault(t.charAt(i), 0) + 1);
}
```

比如：`ABC`

```
A:1 B:1 C:1
```

新建一个窗口用于记录滑动窗口的数值内容

```java
Map<Character, Integer> wins = new HashMap<>();
```

左指针`left`从`0`开始

```java
int left = 0;
```

`right`从`0-i`

```java
int left = 0;
for (int i = 0; i < sLen; i++) {
    // 这样 left ~ i就是窗口
    // 窗口的每一个进来的值就是 i
}
```

主体逻辑，遍历到一个则放入窗口的map中

```java
for (int i = 0; i < sLen; i++) {
    // 放入窗口
    if (tMap.containsKey(s.charAt(i))) {
        wins.put(s.charAt(i), wins.getOrDefault(s.charAt(i), 0) + 1);
    }
}
```

当窗口`map`和符合条件时，并记录一下需要的信息，`left`要右移一位

```java
while (tMap.size() == wins.size() && mapsContains(wins, tMap) && left <= i) {
    // 记录
    if (i - left + 1 < resSize) {
        resSize = i - left + 1;
        res = s.substring(left, i + 1);
    }
    
    char leftValue = s.charAt(left);
    if (wins.containsKey(leftValue)) {
        // 删除
        wins.put(leftValue, wins.get(leftValue) - 1);
    }
    left++;
}
```

这里为什么要用`while`，是因为`left++`之后，窗口可能也符合条件

## 代码

```java
import java.util.HashMap;
import java.util.Map;

//leetcode submit region begin(Prohibit modification and deletion)
class Solution {
    public String minWindow(String s, String t) {
        int sLen = s.length();
        int tLen = t.length();

        if (sLen < tLen) {
            return "";
        }

        Map<Character, Integer> tMap = new HashMap<>();

        for (int i = 0; i < tLen; i++) {
            tMap.put(t.charAt(i), tMap.getOrDefault(t.charAt(i), 0) + 1);
        }

        int left = 0;
        String res = "";
        int resSize = Integer.MAX_VALUE;

        Map<Character, Integer> wins = new HashMap<>();
        for (int i = 0; i < sLen; i++) {
            // 放入窗口
            if (tMap.containsKey(s.charAt(i))) {
                wins.put(s.charAt(i), wins.getOrDefault(s.charAt(i), 0) + 1);
            }
            // 如果包含了 left+1 直到不包含
            while (tMap.size() == wins.size() && mapsContains(wins, tMap) && left <= i) {
                // 记录
                if (i - left + 1 < resSize) {
                    resSize = i - left + 1;
                    res = s.substring(left, i + 1);
                }
                char leftValue = s.charAt(left);
                if (wins.containsKey(leftValue)) {
                    // 删除
                    wins.put(leftValue, wins.get(leftValue) - 1);
                }
                left++;
            }
        }
        return res;
    }

    public Boolean mapsContains(Map<Character, Integer> map1, Map<Character, Integer> map2) {
        if (map1.size() < map2.size()) {
            return false;
        }
        for (Character c :
                map2.keySet()) {
            if (!map1.containsKey(c)) {
                return false;
            }
            if (map1.get(c) < map2.get(c)) {
                return false;
            }
        }

        return true;
    }
}
//leetcode submit region end(Prohibit modification and deletion)

```

```
解答成功:
执行耗时:131 ms,击败了5.02% 的Java用户
内存消耗:38.9 MB,击败了39.75% 的Java用户
```

