---
title: 剑指offer021-删除链表的倒数第n个节点
subtitle: 删除链表的倒数第n个节点
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer021-删除链表的倒数第n个节点

## 题目

<p>给定一个链表，删除链表的倒数第&nbsp;<code>n</code><em>&nbsp;</em>个结点，并且返回链表的头结点。</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>head = [1,2,3,4,5], n = 2
<strong>输出：</strong>[1,2,3,5]
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>head = [1], n = 1
<strong>输出：</strong>[]
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>head = [1,2], n = 1
<strong>输出：</strong>[1]
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li>链表中结点的数目为 <code>sz</code></li>
	<li><code>1 &lt;= sz &lt;= 30</code></li>
	<li><code>0 &lt;= Node.val &lt;= 100</code></li>
	<li><code>1 &lt;= n &lt;= sz</code></li>
</ul>

<p>&nbsp;</p>

<p><strong>进阶：</strong>能尝试使用一趟扫描实现吗？</p>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 19&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/">https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/</a></p>
<div><div>Related Topics</div><div><li>链表</li><li>双指针</li></div></div><br><div><li>👍 14</li><li>👎 0</li></div>

## 思路

双指针，第一个指针开始遍历，当遍历到n是，第二个指针往后移动

## 代码

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {

        ListNode lower = new ListNode(0, head);
        ListNode faster = lower;
        while(faster != null){
            if(n < 0){
                lower = lower.next;
            }
            n--;
            faster = faster.next;
        }
        // 删除lower
        if(lower.next == head){
            head = head.next;
        }else{
            lower.next = lower.next.next;
        }

        return head;
    }
}
```

```
解答成功:
执行耗时:0 ms,击败了100.00% 的Java用户
内存消耗:36.4 MB,击败了50.18% 的Java用户
```

