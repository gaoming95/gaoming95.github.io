---
title: 剑指offer029-排序的循环链表
subtitle: 排序的循环链表
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer029-排序的循环链表

## 题目

<p>给定<strong>循环单调非递减列表</strong>中的一个点，写一个函数向这个列表中插入一个新元素&nbsp;<code>insertVal</code> ，使这个列表仍然是循环升序的。</p>

<p>给定的可以是这个列表中任意一个顶点的指针，并不一定是这个列表中最小元素的指针。</p>

<p>如果有多个满足条件的插入位置，可以选择任意一个位置插入新的值，插入后整个列表仍然保持有序。</p>

<p>如果列表为空（给定的节点是 <code>null</code>），需要创建一个循环有序列表并返回这个节点。否则。请返回原先给定的节点。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<p><img alt="" src="https://assets.leetcode.com/uploads/2019/01/19/example_1_before_65p.jpg" style="height: 149px; width: 250px;" /><br />
&nbsp;</p>

<pre>
<strong>输入：</strong>head = [3,4,1], insertVal = 2
<strong>输出：</strong>[3,4,1,2]
<strong>解释：</strong>在上图中，有一个包含三个元素的循环有序列表，你获得值为 3 的节点的指针，我们需要向表中插入元素 2 。新插入的节点应该在 1 和 3 之间，插入之后，整个列表如上图所示，最后返回节点 3 。

<img alt="" src="https://assets.leetcode.com/uploads/2019/01/19/example_1_after_65p.jpg" style="height: 149px; width: 250px;" />
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>head = [], insertVal = 1
<strong>输出：</strong>[1]
<strong>解释：</strong>列表为空（给定的节点是 <code>null</code>），创建一个循环有序列表并返回这个节点。
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>head = [1], insertVal = 0
<strong>输出：</strong>[1,0]
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>0 &lt;= Number of Nodes &lt;= 5 * 10^4</code></li>
	<li><code><font face="monospace">-10^6 &lt;= Node.val &lt;= 10^6</font></code></li>
	<li><code>-10^6 &lt;=&nbsp;insertVal &lt;= 10^6</code></li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 708&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/insert-into-a-sorted-circular-linked-list/">https://leetcode-cn.com/problems/insert-into-a-sorted-circular-linked-list/</a></p>
<div><div>Related Topics</div><div><li>链表</li></div></div><br><div><li>👍 17</li><li>👎 0</li></div>

## 思路

用数组的想法，这个数字一定是插入到两个数字的中间

如果是自然升序，则如果插入数值在中间范围，就放入

如果是降序，则如果大于最大，或者小于最小，都可以插入

## 代码

```java
class Solution {
    public Node insert(Node head, int insertVal) {
        if (head == null) {
            head = new Node(insertVal);
            head.next = head;
            return head;
        }

        if (head.next == head) {
            Node t = new Node(insertVal);
            head.next = t;
            t.next = head;
            return head;
        }

        Node p1 = head;
        Node p2 = head.next;

        // 记录是否一直递增
        boolean record = true;

        while (p2 != head) {
            // 递增序列
            if (p2.val >= p1.val) {
                if (insertVal <= p2.val && insertVal >= p1.val) {
                    Node t = new Node(insertVal);
                    p1.next = t;
                    t.next = p2;
                    return head;
                }
            } else {
                if(record){
                    if(insertVal >= p1.val || insertVal <= p2.val){
                        Node t = new Node(insertVal);
                        p1.next = t;
                        t.next = p2;
                        return head;
                    }
                }
                record = false;
            }
            p1 = p1.next;
            p2 = p2.next;
        }

        // p2 == head
        if (p2.val >= p1.val) {
            if (insertVal <= p2.val && insertVal >= p1.val) {
                Node t = new Node(insertVal);
                p1.next = t;
                t.next = p2;
                return head;
            }
        }

        if(record){
            Node t = new Node(insertVal);
            p1.next = t;
            t.next = p2;
            return head;
        }
        return head;
    }
}
```

```
解答成功:
执行耗时:0 ms,击败了100.00% 的Java用户
内存消耗:37.6 MB,击败了82.54% 的Java用户
```

