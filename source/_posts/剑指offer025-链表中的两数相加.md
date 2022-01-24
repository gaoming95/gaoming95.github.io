---
title: 剑指offer025-链表中的两数相加
subtitle: 链表中的两数相加
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer025-链表中的两数相加

## 题目

<p>给定两个 <strong>非空链表</strong> <code>l1</code>和 <code>l2</code>&nbsp;来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。</p>

<p>可以假设除了数字 0 之外，这两个数字都不会以零开头。</p>

<p>&nbsp;</p>

<p><strong>示例1：</strong></p>

<p><img alt="" src="https://pic.leetcode-cn.com/1626420025-fZfzMX-image.png" style="width: 302px; " /></p>

<pre>
<strong>输入：</strong>l1 = [7,2,4,3], l2 = [5,6,4]
<strong>输出：</strong>[7,8,0,7]
</pre>

<p><strong>示例2：</strong></p>

<pre>
<strong>输入：</strong>l1 = [2,4,3], l2 = [5,6,4]
<strong>输出：</strong>[8,0,7]
</pre>

<p><strong>示例3：</strong></p>

<pre>
<strong>输入：</strong>l1 = [0], l2 = [0]
<strong>输出：</strong>[0]
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li>链表的长度范围为<code> [1, 100]</code></li>
	<li><code>0 &lt;= node.val &lt;= 9</code></li>
	<li>输入数据保证链表代表的数字无前导 0</li>
</ul>

<p>&nbsp;</p>

<p><strong>进阶：</strong>如果输入链表不能修改该如何处理？换句话说，不能对列表中的节点进行翻转。</p>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 445&nbsp;题相同：<a href="https://leetcode-cn.com/problems/add-two-numbers-ii/">https://leetcode-cn.com/problems/add-two-numbers-ii/</a></p>
<div><div>Related Topics</div><div><li>栈</li><li>链表</li><li>数学</li></div></div><br><div><li>👍 18</li><li>👎 0</li></div>

## 思路

没啥好的思路，暂时保存至数组中，用数据的加法做

## 代码

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if (l1 == null || l2 == null) {
            return null;
        }
        List<Integer> arr1 = new ArrayList<>();
        List<Integer> arr2 = new ArrayList<>();
        while (l1 != null) {
            arr1.add(l1.val);
            l1 = l1.next;
        }

        while (l2 != null) {
            arr2.add(l2.val);
            l2 = l2.next;
        }

        List<Integer> res = addTwoNumbers(arr1, arr2);

        ListNode p = new ListNode(res.get(0));
        ListNode t = p;
        for (int i = 1; i < res.size(); i++) {
            t.next = new ListNode(res.get(i));
            t = t.next;
        }
        return p;
    }

    public List<Integer> addTwoNumbers(List<Integer> arr1, List<Integer> arr2) {
        int m = arr1.size();
        int n = arr2.size();
        int left = m - 1;
        int right = n - 1;
        int d = 0;
        List<Integer> res = new ArrayList();
        while (left >= 0 && right >= 0) {

            int v = d + arr1.get(left) + arr2.get(right);
            int s = v % 10;
            res.add(s);
            d = v / 10;
            left--;
            right--;
        }

        while (left >= 0) {
            int v = d + arr1.get(left);
            int s = v % 10;
            res.add(s);
            d = v / 10;
            left--;
        }

        while (right >= 0) {
            int v = d + arr2.get(right);
            int s = v % 10;
            res.add(s);
            d = v / 10;
            right--;
        }

        if (d > 0) {
            res.add(d);
        }

        List<Integer> res2 = new ArrayList();

        for (int i = res.size() - 1; i >= 0; i--) {
            res2.add(res.get(i));
        }
        return res2;
    }

}
```

```
解答成功:
执行耗时:3 ms,击败了48.72% 的Java用户
内存消耗:38.5 MB,击败了73.26% 的Java用户
```

