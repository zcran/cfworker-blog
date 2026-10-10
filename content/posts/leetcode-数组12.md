---
title: "leetcode-数组12"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 有效的山脉数组

给定一个整数数组 arr，如果它是有效的山脉数组就返回 true，否则返回 false。

让我们回顾一下，如果 arr 满足下述条件，那么它是一个山脉数组：

· arr.length >= 3
· 在 0 < i < arr.length - 1 条件下，存在 i 使得：
    · arr[0] < arr[1] < ... arr[i-1] < arr[i]
    · arr[i] > arr[i+1] > ... > arr[arr.length - 1]
![](../pic/hint_valid_mountain_array.png)


```
impl Solution {
    /// 判断数组是否为有效山脉数组。
    ///
    /// 双指针从两端向中间爬坡：
    /// - `left` 从左向右移动，直到不再严格上升；
    /// - `right` 从右向左移动，直到不再严格下降。
    /// 若两指针相遇于同一位置且该位置不是两端，则为山脉。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn valid_mountain_array(arr: Vec<i32>) -> bool {
        let n = arr.len();
        if n < 3 {
            return false;
        }

        let mut left = 0;
        while left + 1 < n && arr[left] < arr[left + 1] {
            left += 1;
        }

        let mut right = n - 1;
        while right > 0 && arr[right - 1] > arr[right] {
            right -= 1;
        }

        left == right && left != 0 && right != n - 1
    }
}
```
