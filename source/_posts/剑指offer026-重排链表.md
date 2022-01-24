---
title: 剑指offer026-重排链表
subtitle: 重排链表
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer026-重排链表

## 题目

<p>给定一个单链表 <code>L</code><em> </em>的头节点 <code>head</code> ，单链表 <code>L</code> 表示为：</p>

<p><code>&nbsp;L<sub>0&nbsp;</sub>&rarr; L<sub>1&nbsp;</sub>&rarr; &hellip; &rarr; L<sub>n-1&nbsp;</sub>&rarr; L<sub>n&nbsp;</sub></code><br />
请将其重新排列后变为：</p>

<p><code>L<sub>0&nbsp;</sub>&rarr;&nbsp;L<sub>n&nbsp;</sub>&rarr;&nbsp;L<sub>1&nbsp;</sub>&rarr;&nbsp;L<sub>n-1&nbsp;</sub>&rarr;&nbsp;L<sub>2&nbsp;</sub>&rarr;&nbsp;L<sub>n-2&nbsp;</sub>&rarr; &hellip;</code></p>

<p>不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。</p>

<p>&nbsp;</p>

<p><strong>示例 1:</strong></p>

<p><img alt="" src="https://pic.leetcode-cn.com/1626420311-PkUiGI-image.png" style="width: 240px; " /></p>

<pre>
<strong>输入: </strong>head = [1,2,3,4]
<strong>输出: </strong>[1,4,2,3]</pre>

<p><strong>示例 2:</strong></p>

<p><img alt="" src="https://pic.leetcode-cn.com/1626420320-YUiulT-image.png" style="width: 320px; " /></p>

<pre>
<strong>输入: </strong>head = [1,2,3,4,5]
<strong>输出: </strong>[1,5,2,4,3]</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li>链表的长度范围为 <code>[1, 5 * 10<sup>4</sup>]</code></li>
	<li><code>1 &lt;= node.val &lt;= 1000</code></li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 143&nbsp;题相同：<a href="https://leetcode-cn.com/problems/reorder-list/">https://leetcode-cn.com/problems/reorder-list/</a>&nbsp;</p>
<div><div>Related Topics</div><div><li>栈</li><li>递归</li><li>链表</li><li>双指针</li></div></div><br><div><li>👍 20</li><li>👎 0</li></div>

## 思路

先反转链表，再交叉

比如

| 1    | 2    | 3    | 4    |
| ---- | ---- | ---- | ---- |
| 4    | 3    | 2    | 1    |
|      |      |      |      |

1->4 4->2 2->3

## 代码

```java
class Solution {
    public void reorderList(ListNode head) {
        // 反转链表 头插法
        ListNode headCopy = head;
        ListNode rHead = null;
        int num = 0;
        while (head != null) {
            ListNode t = new ListNode(head.val);
            if (rHead == null) {
                rHead = t;
            } else {
                t.next = rHead;
                rHead = t;
            }
            head = head.next;
            num++;
        }
        ListNode h = headCopy;
        ListNode t = headCopy;
        ListNode r = rHead;
        ListNode q = rHead;

        for (int i = 0; i < num-1; i++) {
            if (i % 2 == 0) {
                t = h.next;
                h.next = r;
                h = t;
            } else {
                q = r.next;
                r.next = h;
                r = q;
            }
        }
        h.next = null;
        r.next = null;
    }
}
```

```
解答成功:
执行耗时:2 ms,击败了50.69% 的Java用户
内存消耗:41.8 MB,击败了11.51% 的Java用户
```

