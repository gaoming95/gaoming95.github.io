---
title: 剑指offer033-变位词组
subtitle: 变位词组
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer033-变位词组

## 题目

<p>给定一个字符串数组 <code>strs</code> ，将&nbsp;<strong>变位词&nbsp;</strong>组合在一起。 可以按任意顺序返回结果列表。</p>

<p><strong>注意：</strong>若两个字符串中每个字符出现的次数都相同，则称它们互为变位词。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<pre>
<strong>输入:</strong> strs = <code>[&quot;eat&quot;, &quot;tea&quot;, &quot;tan&quot;, &quot;ate&quot;, &quot;nat&quot;, &quot;bat&quot;]</code>
<strong>输出: </strong>[[&quot;bat&quot;],[&quot;nat&quot;,&quot;tan&quot;],[&quot;ate&quot;,&quot;eat&quot;,&quot;tea&quot;]]</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong> strs = <code>[&quot;&quot;]</code>
<strong>输出: </strong>[[&quot;&quot;]]
</pre>

<p><strong>示例 3:</strong></p>

<pre>
<strong>输入:</strong> strs = <code>[&quot;a&quot;]</code>
<strong>输出: </strong>[[&quot;a&quot;]]</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= strs.length &lt;= 10<sup>4</sup></code></li>
	<li><code>0 &lt;= strs[i].length &lt;= 100</code></li>
	<li><code>strs[i]</code>&nbsp;仅包含小写字母</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 49&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/group-anagrams/">https://leetcode-cn.com/problems/group-anagrams/</a></p>
<div><div>Related Topics</div><div><li>哈希表</li><li>字符串</li><li>排序</li></div></div><br><div><li>👍 11</li><li>👎 0</li></div>

## 思路

先排序，分桶收集

## 代码

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {

        List<List<String>> res = new ArrayList<>();
        if (strs.length == 0) {
            List<String> a = Arrays.asList("");
            res.add(a);
            return res;
        }

        if (strs.length == 1) {
            List<String> a = Arrays.asList(strs);
            res.add(a);
            return res;
        }

        Map<String, List<String>> map = new HashMap<>();
        for (int i = 0; i < strs.length; i++) {
            String str = strs[i];
            String key;
            if (str == "") {
                key = "";
            } else {
                char[] chars = str.toCharArray();
                Arrays.sort(chars);
                key = Arrays.toString(chars);
            }
            if (map.containsKey(key)) {
                map.get(key).add(strs[i]);
            } else {
                List<String> s = new ArrayList<>();
                s.add(strs[i]);
                map.put(key, s);
            }
        }

        for (String key : map.keySet()) {
            List<String> t = map.get(key);
            res.add(t);
        }
        return res;
    }
}
```

