---
title: "leetcode-计数65"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 按位与结果大于零的最长组合

对数组 nums 执行 按位与 相当于对数组 nums 中的所有整数执行 按位与 。

例如，对 nums = [1, 5, 3] 来说，按位与等于 1 & 5 & 3 = 1 。

同样，对 nums = [7] 而言，按位与等于 7 。

给你一个正整数数组 candidates 。计算 candidates 中的数字每种组合下 按位与 的结果。

返回按位与结果大于 0 的 最长 组合的长度。


```
impl Solution {
    pub fn largest_combination(candidates: Vec<i32>) -> i32 {
        // 对于每个二进制位，统计该位为 1 的元素个数
        // 选择同一位置都为 1 的所有元素，它们的按位与结果在该位为 1
        let mut max_len = 0;

        // 题目约束 candidates[i] <= 10^7 < 2^24
        for bit in 0..24 {
            let count = candidates
                .iter()
                .filter(|&&num| (num >> bit) & 1 == 1)
                .count();
            max_len = max_len.max(count);
        }

        max_len as i32
    }
}
```
