---
title: "leetcode-数组50"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 特殊数组 I

如果数组的每一对相邻元素都是两个奇偶性不同的数字，则该数组被认为是一个 特殊数组。换句话说，每一对中的元素 必须 有一个是偶数，另一个是奇数。

你有一个整数数组 nums。如果 nums 是一个 特殊数组 ，返回 true，否则返回 false。


```
impl Solution {
    /// 判断数组是否为特殊数组：每对相邻元素奇偶性不同。
    ///
    /// 若相邻两元素模 2 相等（同为奇或同为偶），则不是特殊数组。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn is_array_special(nums: Vec<i32>) -> bool {
        nums.windows(2).all(|w| (w[0] & 1) != (w[1] & 1))
    }
}
```
