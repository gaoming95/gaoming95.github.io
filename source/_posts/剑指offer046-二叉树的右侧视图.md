---
title: 剑指offer046-二叉树的右侧视图
subtitle: 二叉树的右侧视图
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer046-二叉树的右侧视图

## 题目

<p>给定一个二叉树的 <strong>根节点</strong> <code>root</code>，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<p><img src="https://assets.leetcode.com/uploads/2021/02/14/tree.jpg" style="width: 270px; " /></p>

<pre>
<strong>输入:</strong>&nbsp;[1,2,3,null,5,null,4]
<strong>输出:</strong>&nbsp;[1,3,4]
</pre>

<p><strong>示例 2:</strong></p>

<pre>
<strong>输入:</strong>&nbsp;[1,null,3]
<strong>输出:</strong>&nbsp;[1,3]
</pre>

<p><strong>示例 3:</strong></p>

<pre>
<strong>输入:</strong>&nbsp;[]
<strong>输出:</strong>&nbsp;[]
</pre>

<p>&nbsp;</p>

<p><strong>提示:</strong></p>

<ul>
	<li>二叉树的节点个数的范围是 <code>[0,100]</code></li>
	<li><meta charset="UTF-8" /><code>-100&nbsp;&lt;= Node.val &lt;= 100</code>&nbsp;</li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 199&nbsp;题相同：<a href="https://leetcode-cn.com/problems/binary-tree-right-side-view/">https://leetcode-cn.com/problems/binary-tree-right-side-view/</a></p>
<div><div>Related Topics</div><div><li>树</li><li>深度优先搜索</li><li>广度优先搜索</li><li>二叉树</li></div></div><br><div><li>👍 15</li><li>👎 0</li></div>

## 思路

（1）一开始以为是右侧的节点，直接访问右侧节点了，写出如下代码

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        findRight(root, res);
        return res;
    }

    void findRight(TreeNode node, List<Integer> res) {
        if (node == null) {
            return;
        }
        res.add(node.val);
        if (node.right != null) {
            findRight(node.right, res);
        }
    }
}
```

这个是有问题的，因为右侧节点可能不存在，这样右侧是能看到左侧的节点的

（2）调整过来就层次遍历了

## 代码

```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {

        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {

                TreeNode t = queue.poll();

                if(t.left != null){
                    queue.offer(t.left);
                }

                if(t.right != null){
                    queue.offer(t.right);
                }

                if(i == size - 1){
                    res.add(t.val);
                }
            }
        }
        return res;
    }
}
```

```
解答成功:
执行耗时:1 ms,击败了87.56% 的Java用户
内存消耗:37.1 MB,击败了35.01% 的Java用户
```

