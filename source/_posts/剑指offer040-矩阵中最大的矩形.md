---
title: 剑指offer040-矩阵中的最大的矩形
subtitle: 矩阵中的最大的矩形
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer040-矩阵中的最大的矩形

## 题目

<p>给定一个由&nbsp;<code>0</code> 和 <code>1</code>&nbsp;组成的矩阵 <code>matrix</code>&nbsp;，找出只包含 <code>1</code> 的最大矩形，并返回其面积。</p>

<p><strong>注意：</strong>此题 <code>matrix</code>&nbsp;输入格式为一维 <code>01</code> 字符串数组。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg" style="width: 402px; height: 322px;" /></p>

<pre>
<strong>输入：</strong>matrix = ["10100","10111","11111","10010"]
<strong>输出：</strong>6
<strong>解释：</strong>最大矩形如上图所示。
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>matrix = []
<strong>输出：</strong>0
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>matrix = ["0"]
<strong>输出：</strong>0
</pre>

<p><strong>示例 4：</strong></p>

<pre>
<strong>输入：</strong>matrix = ["1"]
<strong>输出：</strong>1
</pre>

<p><strong>示例 5：</strong></p>

<pre>
<strong>输入：</strong>matrix = ["00"]
<strong>输出：</strong>0
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>rows == matrix.length</code></li>
	<li><code>cols == matrix[0].length</code></li>
	<li><code>0 &lt;= row, cols &lt;= 200</code></li>
	<li><code>matrix[i][j]</code> 为 <code>'0'</code> 或 <code>'1'</code></li>
</ul>

<p>&nbsp;</p>

<p>注意：本题与主站 85 题相同（输入参数格式不同）：&nbsp;<a href="https://leetcode-cn.com/problems/maximal-rectangle/">https://leetcode-cn.com/problems/maximal-rectangle/</a></p>
<div><div>Related Topics</div><div><li>栈</li><li>数组</li><li>动态规划</li><li>矩阵</li><li>单调栈</li></div></div><br><div><li>👍 16</li><li>👎 0</li></div>

## 思路

参考直方图的最大面积

需要计算每一块的最大面积

## 代码

```java
class Solution {
    public int maximalRectangle(String[] matrix) {
        if (matrix.length == 0) {
            return 0;
        }

        int m = matrix.length;
        int n = matrix[0].length();

        int[][] dp = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                dp[i][j] = Integer.valueOf(String.valueOf(matrix[i].charAt(j)));
            }
        }

        for (int i = 1; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if(dp[i][j] != 0){
                    dp[i][j] = dp[i - 1][j] + dp[i][j];
                }
            }
        }

        int res = Integer.MIN_VALUE;

        for (int i = 0; i < m; i++) {
            int v = findMax(dp[i]);
            res = Math.max(res, v);
        }

        return res;

    }

    int findMax(int[] arr) {

        Stack<Integer> stack = new Stack<>();
        stack.push(-1);
        int res = Integer.MIN_VALUE;
        for (int i = 0; i < arr.length; i++) {

            while (stack.peek() != -1 && arr[i] < arr[stack.peek()]) {
                int height = arr[stack.pop()];
                int width = i - stack.peek() - 1;
                res = Math.max(res, height * width);
            }
            stack.push(i);
        }

        while (stack.peek() != -1) {
            int height = arr[stack.pop()];
            int width = arr.length - stack.peek() - 1;
            res = Math.max(res, height * width);
        }
        return res;
    }
}
```

