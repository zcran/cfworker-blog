---
title: "leetcode-数组39"
date: 2026-10-10T20:38:43+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 找出可整除性得分最大的整数

给你两个整数数组 nums 和 divisors 。

divisors[i] 的 可整除性得分 等于满足 nums[j] 能被 divisors[i] 整除的下标 j 的数量。

返回 可整除性得分 最大的整数 divisors[i] 。如果有多个整数具有最大得分，则返回数值最小的一个。




```
impl Solution {
    /// 返回可整除性得分最大的除数；若并列，返回数值最小的除数。
    ///
    /// 对每个 `divisors[i]`，统计 `nums` 中能被它整除的元素个数，
    /// 遍历过程中维护「最大得分」及对应的最小除数。
    ///
    /// 时间复杂度 O(m·n)，空间复杂度 O(1)。
    pub fn max_div_score(nums: Vec<i32>, divisors: Vec<i32>) -> i32 {
        let mut best_score = -1;
        let mut best_divisor = 0;

        for &d in &divisors {
            let score = nums.iter().filter(|&&x| x % d == 0).count() as i32;
            if score > best_score || (score == best_score && d < best_divisor) {
                best_score = score;
                best_divisor = d;
            }
        }

        best_divisor
    }
}
```
