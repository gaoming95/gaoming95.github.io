---
title: 剑指offer050-向下的路径节点之和
subtitle: 向下的路径节点之和
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer050-向下的路径节点之和

## 题目

<p>给定一个二叉树的根节点 <code>root</code>&nbsp;，和一个整数 <code>targetSum</code> ，求该二叉树里节点值之和等于 <code>targetSum</code> 的 <strong>路径</strong> 的数目。</p>

<p><strong>路径</strong> 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<p><img src="https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg" style="width: 452px; " /></p>

<pre>
<strong>输入：</strong>root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
<strong>输出：</strong>3
<strong>解释：</strong>和等于 8 的路径有 3 条，如图所示。
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
<strong>输出：</strong>3
</pre>

<p>&nbsp;</p>

<p><strong>提示:</strong></p>

<ul>
	<li>二叉树的节点个数的范围是 <code>[0,1000]</code></li>
	<li><meta charset="UTF-8" /><code>-10<sup><span style="font-size: 9.449999809265137px;">9</span></sup>&nbsp;&lt;= Node.val &lt;= 10<sup><span style="font-size: 9.449999809265137px;">9</span></sup></code>&nbsp;</li>
	<li><code>-1000&nbsp;&lt;= targetSum&nbsp;&lt;= 1000</code>&nbsp;</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 437&nbsp;题相同：<a href="https://leetcode-cn.com/problems/path-sum-iii/">https://leetcode-cn.com/problems/path-sum-iii/</a></p>
<div><div>Related Topics</div><div><li>树</li><li>深度优先搜索</li><li>二叉树</li></div></div><br><div><li>👍 13</li><li>👎 0</li></div>

## 思路

递归

## 代码

```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {

        if (root == null) {
            return 0;
        }
        // 以当前节点为根节点
        int now = rootSum(root, targetSum);
        // 考虑左右节点
        int left = pathSum(root.left, targetSum);
        int right = pathSum(root.right, targetSum);
        return now + left + right;
    }

    int rootSum(TreeNode root, int targetSum) {

        if (root == null) {
            return 0;
        }

        int res = 0;
        if (root.val == targetSum) {
            res++;
        }

        int left = rootSum(root.left, targetSum - root.val);
        int right = rootSum(root.right, targetSum - root.val);
        return res + left + right;
    }
}
```

