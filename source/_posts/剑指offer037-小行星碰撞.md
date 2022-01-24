---
title: 剑指offer037-小行星碰撞
subtitle: 小行星碰撞
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer037-小行星碰撞

## 题目

<p>给定一个整数数组 <code>asteroids</code>，表示在同一行的小行星。</p>

<p>对于数组中的每一个元素，其绝对值表示小行星的大小，正负表示小行星的移动方向（正表示向右移动，负表示向左移动）。每一颗小行星以相同的速度移动。</p>

<p>找出碰撞后剩下的所有小行星。碰撞规则：两个行星相互碰撞，较小的行星会爆炸。如果两颗行星大小相同，则两颗行星都会爆炸。两颗移动方向相同的行星，永远不会发生碰撞。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>asteroids = [5,10,-5]
<strong>输出：</strong>[5,10]
<b>解释：</b>10 和 -5 碰撞后只剩下 10 。 5 和 10 永远不会发生碰撞。</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>asteroids = [8,-8]
<strong>输出：</strong>[]
<b>解释：</b>8 和 -8 碰撞后，两者都发生爆炸。</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>asteroids = [10,2,-5]
<strong>输出：</strong>[10]
<b>解释：</b>2 和 -5 发生碰撞后剩下 -5 。10 和 -5 发生碰撞后剩下 10 。</pre>

<p><strong>示例 4：</strong></p>

<pre>
<strong>输入：</strong>asteroids = [-2,-1,1,2]
<strong>输出：</strong>[-2,-1,1,2]
<b>解释</b><strong>：</strong>-2 和 -1 向左移动，而 1 和 2 向右移动。 由于移动方向相同的行星不会发生碰撞，所以最终没有行星发生碰撞。 </pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>2 &lt;= asteroids.length&nbsp;&lt;= 10<sup>4</sup></code></li>
	<li><code>-1000 &lt;= asteroids[i] &lt;= 1000</code></li>
	<li><code>asteroids[i] != 0</code></li>
</ul>

<p>&nbsp;</p>

<p><meta charset="UTF-8" />注意：本题与主站 735&nbsp;题相同：&nbsp;<a href="https://leetcode-cn.com/problems/asteroid-collision/">https://leetcode-cn.com/problems/asteroid-collision/</a></p>
<div><div>Related Topics</div><div><li>栈</li><li>数组</li></div></div><br><div><li>👍 12</li><li>👎 0</li></div>

## 思路

一开始想用两个栈实现，一个栈存负数的下标，一个栈存正数的下标，写到后面越来越烦

慢慢觉得问题可以简化

首先考虑开头是负数的场景，如 `-1 -2 -3 1 2 3`

其实前面的负数已经走到头了，没有人会再和他们融合

所以记录开始位置

```java
int index = 0;
while (index < asteroids.length && asteroids[index] < 0) {
    index++;
}
```

从第一个正数开始，我们维护一个为正数的栈（除非有一个很大的负数，导致所有正数都被消除）

当数字为正数时，直接入栈即可

当数据为负数时，我们要从栈顶中拿出数(pop)与(i)进行PK

比如 10，2，-5

|      |                         |
| ---- | ----------------------- |
|      | （3）-5即将入栈，开始PK |
| 2    | （2）2入栈              |
| 10   | （1）10入栈             |

直到我们可以消除-5，获取-5消除了所有的正数，到了栈底部

```java
Stack<Integer> stack = new Stack<>();

for (int i = index; i < asteroids.length; i++) {
    if (asteroids[i] > 0) {
        // 如果是正数，直接入栈 10入栈，2入栈
        stack.push(asteroids[i]);
    } else {
        // 如果是负数 t=-5
        int t = asteroids[i];
        // 每次pk最后选择的数字如果还是小于0，我们需要继续比较
        while (t < 0 && !stack.isEmpty()) {
            // 拿出栈中的第一个正数 2
            Integer peek = stack.peek();
            // 如果第一个数是负数，说明已经结束了，我们不需要比较
            if (peek < 0) {
                break;
            } else if (peek > 0) {
                // 如果是正数
                // PK一下
                t = pick(peek, t);
                if (t == 0) {
                    // 如果相等，说明两者消除了，我们放弃这两个数
                    stack.pop();
                    break;
                } else if (t > 0) {
                    // 如果t大于0，说明队列中的数赢了，我们放弃负数
                    break;
                } else if (t < 0) {
                    // 如果t小于0，说明队列中数输了，我们移除2，继续将-5和10进行比较
                    stack.pop();
                }
            }
        }
    }
}
```

除此之外，当一直比较下去，会出现最后的负数没有加入进去

比如10，2，-100

-100经过两次PK，都赢了，我们应该把它放入队列

```java
for (int i = index; i < asteroids.length; i++) {
    if (asteroids[i] > 0) {
        stack.push(asteroids[i]);
    } else {
        int t = asteroids[i];
        boolean flag = true;
        while (t < 0 && !stack.isEmpty()) {
            Integer peek = stack.peek();
            if (peek < 0) {
                break;
            } else if (peek > 0) {
                t = pick(peek, t);
                if (t == 0) {
                    stack.pop();
                    flag = false;
                    break;
                } else if (t > 0) {
                    flag = false;
                    break;
                } else if (t < 0) {
                    stack.pop();
                }
            }
        }
        if (flag) {
            stack.push(asteroids[i]);
        }
    }
}
```

最后统计一下

```java
int n = index + stack.size();
int[] res = new int[n];
for (int i = 0; i < index; i++) {
    res[i] = asteroids[i];
}
while (n > index) {
    res[n - 1] = stack.pop();
    n = n - 1;
}
return res;
```

## 代码

```java
class Solution {
    public int[] asteroidCollision(int[] asteroids) {
        int index = 0;
        while (index < asteroids.length && asteroids[index] < 0) {
            index++;
        }

        Stack<Integer> stack = new Stack<>();

        for (int i = index; i < asteroids.length; i++) {
            if (asteroids[i] > 0) {
                stack.push(asteroids[i]);
            } else {
                int t = asteroids[i];
                boolean flag = true;
                while (t < 0 && !stack.isEmpty()) {
                    Integer peek = stack.peek();
                    if (peek < 0) {
                        break;
                    } else if (peek > 0) {
                        t = pick(peek, t);
                        if (t == 0) {
                            stack.pop();
                            flag = false;
                            break;
                        } else if (t > 0) {
                            flag = false;
                            break;
                        } else if (t < 0) {
                            stack.pop();
                        }
                    }
                }
                if (flag) {
                    stack.push(asteroids[i]);
                }
            }
        }

        int n = index + stack.size();
        int[] res = new int[n];
        for (int i = 0; i < index; i++) {
            res[i] = asteroids[i];
        }
        while (n > index) {
            res[n - 1] = stack.pop();
            n = n - 1;
        }
        return res;
    }

    public int pick(int x, int y) {
        if (x == -y) {
            return 0;
        } else if (x > 0 && y < 0) {
            return x > -y ? x : y;
        } else if (x < 0 && y > 0) {
            return y > -x ? y : x;
        } else {
            return 0;
        }
    }
}
```

```
解答成功:
执行耗时:5 ms,击败了77.12% 的Java用户
内存消耗:38.9 MB,击败了87.64% 的Java用户
```

