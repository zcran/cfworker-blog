---
title: "leetcode-位运算52"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 收集元素的最少操作次数

给你一个正整数数组 nums 和一个整数 k 。

一次操作中，你可以将数组的最后一个元素删除，将该元素添加到一个集合中。

请你返回收集元素 1, 2, ..., k 需要的 最少操作次数 。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>, k: i32) -> i32 {
        // 从数组末尾往前取，直到集合中已包含 1..=k 全部数字为止。
        // 用位掩码记录已收集的数字：mask 的第 x 位为 1 表示数字 x 已收集。
        let target: u64 = (1u64 << (k + 1)) - 2; // 第 1..=k 位为 1
        let n = nums.len();
        let mut mask: u64 = 0;

        for i in (0..n).rev() {
            mask |= 1u64 << nums[i];
            if mask & target == target {
                return (n - i) as i32;
            }
        }

        unreachable!() // 题目保证一定能收集齐
    }
}
```
