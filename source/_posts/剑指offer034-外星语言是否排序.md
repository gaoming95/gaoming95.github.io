---
title: 剑指offer034-外星语言是否排序
subtitle: 外星语言是否排序
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer034-外星语言是否排序

## 题目


<p>某种外星语也使用英文小写字母，但可能顺序 <code>order</code> 不同。字母表的顺序（<code>order</code>）是一些小写字母的排列。</p>

<p>给定一组用外星语书写的单词 <code>words</code>，以及其字母表的顺序 <code>order</code>，只有当给定的单词在这种外星语中按字典序排列时，返回 <code>true</code>；否则，返回 <code>false</code>。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>words = [&quot;hello&quot;,&quot;leetcode&quot;], order = &quot;hlabcdefgijkmnopqrstuvwxyz&quot;
<strong>输出：</strong>true
<strong>解释：</strong>在该语言的字母表中，&#39;h&#39; 位于 &#39;l&#39; 之前，所以单词序列是按字典序排列的。</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>words = [&quot;word&quot;,&quot;world&quot;,&quot;row&quot;], order = &quot;worldabcefghijkmnpqstuvxyz&quot;
<strong>输出：</strong>false
<strong>解释：</strong>在该语言的字母表中，&#39;d&#39; 位于 &#39;l&#39; 之后，那么 words[0] &gt; words[1]，因此单词序列不是按字典序排列的。</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>words = [&quot;apple&quot;,&quot;app&quot;], order = &quot;abcdefghijklmnopqrstuvwxyz&quot;
<strong>输出：</strong>false
<strong>解释：</strong>当前三个字符 &quot;app&quot; 匹配时，第二个字符串相对短一些，然后根据词典编纂规则 &quot;apple&quot; &gt; &quot;app&quot;，因为 &#39;l&#39; &gt; &#39;&empty;&#39;，其中 &#39;&empty;&#39; 是空白字符，定义为比任何其他字符都小（<a href="https://baike.baidu.com/item/%E5%AD%97%E5%85%B8%E5%BA%8F" target="_blank">更多信息</a>）。
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= words.length &lt;= 100</code></li>
	<li><code>1 &lt;= words[i].length &lt;= 20</code></li>
	<li><code>order.length == 26</code></li>
	<li>在&nbsp;<code>words[i]</code>&nbsp;和&nbsp;<code>order</code>&nbsp;中的所有字符都是英文小写字母。</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 953&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/verifying-an-alien-dictionary/">https://leetcode-cn.com/problems/verifying-an-alien-dictionary/</a></p>
<div><div>Related Topics</div><div><li>数组</li><li>哈希表</li><li>字符串</li></div></div><br><div><li>👍 11</li><li>👎 0</li></div>

## 思路

这题目没什么特别的思路，就是一个一个比较

首先用`map`定义字母的顺序

```java
Map<Character, Integer> orderMap = new HashMap<>();
for (int i = 0; i < order.length(); i++) {
    orderMap.put(order.charAt(i), i);
}
```

因为判断数组单词是否字母序，且如果`A<B,B<C`，那么`A<C`，所以只需要判断相邻的单词是否满足A<B即可

```java
for (int i = 0; i < words.length - 1; i++) {
    String word1 = words[i];
    String word2 = words[i + 1];
    // 比较 word1 和 word2 即可
}
```

单词之间的相互比较，如果比如

| a    | b    | d    |
| ---- | ---- | ---- |
| a    | c    | e    |

其实如果对应位置上的单词相等，则判断下一个

如果对应位置上的单词不相等，能判断两个单词的大小关系，但是对于整个数组，二者的大小关系不能决定整个数组的大小关系

存在一种情况，如果长度不等，且前缀都是一致的

| a    | b    | c    |
| ---- | ---- | ---- |
| a    | b    |      |

很显然ab < abc，但是由于可能忽略这种情况，导致比较到最后都是相等的，直接返回AB相等

这种情况可以设置没有元素的地方为-1

```java
// word1 和 word2 的最大长度
int index = Math.max(word1.length(), word2.length());
for (int j = 0; j < index; j++) {
    // 单词1的字母序，为空的时候为-1
    int v1 = j < word1.length() ? orderMap.get(word1.charAt(j)) : -1;
    // 单词2的字母序，为空的时候为-1
    int v2 = j < word2.length() ? orderMap.get(word2.charAt(j)) : -1;
    // 如果 单词1 > 单词2,直接返回False 
    if (v1 > v2) {
        return false;
    } else if (v1 < v2) {
        // 反之可以继续比较
        break;
    }
}
```

## 代码

```java
class Solution {
    public boolean isAlienSorted(String[] words, String order) {
        Map<Character, Integer> orderMap = new HashMap<>();
        for (int i = 0; i < order.length(); i++) {
            orderMap.put(order.charAt(i), i);
        }

        for (int i = 0; i < words.length - 1; i++) {
            String word1 = words[i];
            String word2 = words[i + 1];
            int index = Math.max(word1.length(), word2.length());
            for (int j = 0; j < index; j++) {

                int v1 = j < word1.length() ? orderMap.get(word1.charAt(j)) : -1;
                int v2 = j < word2.length() ? orderMap.get(word2.charAt(j)) : -1;

                if (v1 > v2) {
                    return false;
                } else if (v1 < v2) {
                    break;
                }
            }
        }
        return true;
    }
}
```

