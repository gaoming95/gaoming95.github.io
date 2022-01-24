---
title: 剑指offer006-排序数组中两个数字之和
subtitle: 排序数组中两个数字之和
date: 2020-01-01
author: 高明
tags:
	- 剑指offer
---



# 剑指offer006-排序数组中两个数字之和

## 题目

<p>给定一个已按照<strong><em> </em>升序排列&nbsp; </strong>的整数数组&nbsp;<code>numbers</code> ，请你从数组中找出两个数满足相加之和等于目标数&nbsp;<code>target</code> 。</p>

<p>函数应该以长度为 <code>2</code> 的整数数组的形式返回这两个数的下标值<em>。</em><code>numbers</code> 的下标 <strong>从 0&nbsp;开始计数</strong> ，所以答案数组应当满足 <code>0&nbsp;&lt;= answer[0] &lt; answer[1] &lt;&nbsp;numbers.length</code>&nbsp;。</p>

<p>假设数组中存在且只存在一对符合条件的数字，同时一个数字不能使用两次。</p>

<p>&nbsp;</p>

<p><strong>示例 1：</strong></p>

<pre>
<strong>输入：</strong>numbers = [1,2,4,6,10], target = 8
<strong>输出：</strong>[1,3]
<strong>解释：</strong>2 与 6 之和等于目标数 8 。因此 index1 = 1, index2 = 3 。
</pre>

<p><strong>示例 2：</strong></p>

<pre>
<strong>输入：</strong>numbers = [2,3,4], target = 6
<strong>输出：</strong>[0,2]
</pre>

<p><strong>示例 3：</strong></p>

<pre>
<strong>输入：</strong>numbers = [-1,0], target = -1
<strong>输出：</strong>[0,1]
</pre>

<p>&nbsp;</p>

<p><strong>提示：</strong></p>

<ul>
	<li><code>2 &lt;= numbers.length &lt;= 3 * 10<sup>4</sup></code></li>
	<li><code>-1000 &lt;= numbers[i] &lt;= 1000</code></li>
	<li><code>numbers</code> 按 <strong>递增顺序</strong> 排列</li>
	<li><code>-1000 &lt;= target &lt;= 1000</code></li>
	<li>仅存在一个有效答案</li>
</ul>
## 思路

这个双指针，就不说了

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        while (left < right) {
            int s = numbers[left] + numbers[right];
            if (s < target) {
                left += 1;
            } else if (s > target) {
                right -= 1;
            } else {
                return new int[]{left, right};
            }
        }
        return new int[]{0, 0};
    }
}
```

```
解答成功:
执行耗时:0 ms,击败了100.00% 的Java用户
内存消耗:38.6 MB,击败了61.05% 的Java用户
```

```
BfsUploadResult iconUploadResult = null;

        try {
            iconUploadResult = bfsService.upload(APP_ICON, info.getPackageName() + "_icon.gif",
                    IOUtils.toByteArray(info.getIconInputStream()));
        } catch (Exception e) {
            LOGGER.error("upload icon failed:" + Throwables.getStackTraceAsString(e));
            setFailed("上传app图标失败");
            return null;
        } finally {
            IOUtils.closeQuietly(info.getIconInputStream());
        }

        if (iconUploadResult == null) {
            setFailed("上传app图标失败");
            return null;
        }

        return iconUploadResult.getUrl();
```

