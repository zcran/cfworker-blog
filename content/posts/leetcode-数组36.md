---
title: "leetcode-数组36"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 找到最接近 0 的数字

给你一个长度为 n 的整数数组 nums ，请你返回 nums 中最 接近 0 的数字。如果有多个答案，请你返回它们中的 最大值 。




```
impl Solution {
    /// 返回数组中最接近 0 的数字；若绝对值相同，返回较大的那个。
    ///
    /// 一次遍历，维护「当前最小绝对值」`best_abs` 与「对应答案」`best`：
    /// - 若 `abs(x) < best_abs`，更新两者；
    /// - 若 `abs(x) == best_abs`，保留较大的 `x`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn find_closest_number(nums: Vec<i32>) -> i32 {
        let mut best = nums[0];
        let mut best_abs = nums[0].abs();

        for &x in &nums[1..] {
            let abs = x.abs();
            if abs < best_abs || (abs == best_abs && x > best) {
                best = x;
                best_abs = abs;
            }
        }

        best
    }
}
```
