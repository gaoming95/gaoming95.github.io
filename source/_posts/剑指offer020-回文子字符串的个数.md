---
title: 剑指offer020-回文子字符串的个数
subtitle: 回文子字符串的个数
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer020-回文子字符串的个数

## 题目

<p>给定一个字符串 <code>s</code> ，请计算这个字符串中有多少个回文子字符串。</p>

<p>具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>s = "abc"
<strong>输出：</strong>3
<strong>解释：</strong>三个回文子串: "a", "b", "c"
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>s =<strong> </strong>"aaa"
<strong>输出：</strong>6
<strong>解释：</strong>6个回文子串: "a", "a", "a", "aa", "aa", "aaa"</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>1 &lt;= s.length &lt;= 1000</code></li>
	<li><code>s</code> 由小写英文字母组成</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 647 题相同：<a href="https://leetcode-cn.com/problems/palindromic-substrings/">https://leetcode-cn.com/problems/palindromic-substrings/</a>&nbsp;</p>
<div><div>Related Topics</div><div><li>字符串</li><li>动态规划</li></div></div><br><div><li>👍 16</li><li>👎 0</li></div>

## 思路

乍一想感觉是动态规划，我们定义`dp[i][j]`为s的`i-j`是否回文，这样我们只要统计`dp`数组中`1`的个数就好

很明显，对于每一个单个字符来说，`s[i]`本身就是回文，一个字符的

我们考虑`dp[i][j]`的递推关系式

| 1    |      |      |      |      |      |
| ---- | ---- | ---- | ---- | ---- | ---- |
|      | 1    |      |      |      |      |
|      |      | 1    |      |      |      |
|      |      |      | 1    |      |      |
|      |      |      |      | 1    |      |
|      |      |      |      |      | 1    |

`dp[i][j]`

如果`s[i] == s[j]`的话，`dp[i][j]`=`dp[i+1][j-1]`

如果`s[i] == s[j]`,`dp[i][j]`=`0`

我们初始化dp数组

```java
int[][] dp = new int[m][m];
int res = 0;
// 初始化
for (int i = 0; i < m; i++) {
    dp[i][i] = 1;
    ++res;
}
```

递推关系式`dp[i][j]`=`dp[i+1][j-1]`是从右上往左下走，因此我们需要从左下到右上填充数据

还考虑到如果只有两个字符，会出现内部字符串为空或者`left<right`的情况

```java
for (int i = m - 1; i >= 0; i--) {
    for (int j = i + 1; j < m; j++) {
        int left = i + 1;
        int right = j - 1;
        // 比如abc i->a j->c 则left = right -> b
        // 比如ab i->a j->b 则left > right
        // 这两种情况都只要判断 s[i] == s[j]
        if (left >= right) {
            if (s.charAt(i) == s.charAt(j)) {
                dp[i][j] = 1;
                ++res;
            }
        } else {
            // 只有两侧相等且内部字符串为回文
            if (s.charAt(i) == s.charAt(j) && dp[left][right] == 1) {
                dp[i][j] = 1;
                ++res;
            }
        }
    }
}
```

```
解答成功:
执行耗时:12 ms,击败了21.44% 的Java用户
内存消耗:42.4 MB,击败了5.02% 的Java用户
```

## 代码

```java
class Solution {
    public int countSubstrings(String s) {
        int m = s.length();

        if (m == 1) {
            return 1;
        }

        if (m == 2) {
            return 2 + (s.charAt(0) == s.charAt(1) ? 1 : 0);
        }

        int[][] dp = new int[m][m];
        int res = 0;
        // 初始化
        for (int i = 0; i < m; i++) {
            dp[i][i] = 1;
            ++res;
        }

        for (int i = m - 1; i >= 0; i--) {
            for (int j = i + 1; j < m; j++) {
                int left = i + 1;
                int right = j - 1;
                // 比如abc i->a j->c 则left = right -> b
                // 比如ab i->a j->b 则left > right
                // 这两种情况都只要判断 s[i] == s[j]
                if (left >= right) {
                    if (s.charAt(i) == s.charAt(j)) {
                        dp[i][j] = 1;
                        ++res;
                    }
                } else {
                    if (s.charAt(i) == s.charAt(j) && dp[left][right] == 1) {
                        dp[i][j] = 1;
                        ++res;
                    }
                }
            }
        }
        return res;
    }
}
```

