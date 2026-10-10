---
title: "leetcode-排序13"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 至少是其他数字两倍的最大数

给你一个整数数组 nums ，其中总是存在 唯一的 一个最大整数 。

请你找出数组中的最大元素并检查它是否 至少是数组中每个其他数字的两倍 。如果是，则返回 最大元素的下标 ，否则返回 -1 。


```
impl Solution {
    /// 找出最大元素，若它至少是其余每个元素的两倍，返回其下标，否则返回 -1
    ///
    /// 思路：一次遍历维护「最大值」和「次大值」。
    /// 若最大值 ≥ 2 × 次大值，则它必然也 ≥ 2 × 其余所有元素，返回其下标。
    /// 题目保证最大元素唯一，故只需比较这两者。
    /// 时间 O(n)，额外空间 O(1)。
    pub fn dominant_index(nums: Vec<i32>) -> i32 {
        let mut max = i32::MIN;      // 最大值
        let mut second = i32::MIN;   // 次大值
        let mut idx = 0;             // 最大值下标

        for (i, &x) in nums.iter().enumerate() {
            if x > max {
                second = max;
                max = x;
                idx = i;
            } else if x > second {
                second = x;
            }
        }

        // 最大元素唯一；次大值可能不存在（n=1），此时 MIN 的 2 倍会溢出，需特判
        if second == i32::MIN || max >= second * 2 {
            idx as i32
        } else {
            -1
        }
    }
}
```
