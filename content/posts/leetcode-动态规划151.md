---
title: "leetcode-动态规划151"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最大化交错和为 K 的子序列乘积

给你一个整数数组 nums 和两个整数 k 与 limit，你的任务是找到一个非空的 子序列，满足以下条件：

它的 交错和 等于 k。

在乘积 不超过 limit 的前提下，最大化 其所有数字的乘积。

返回满足条件的子序列的 乘积 。如果不存在这样的子序列，则返回 -1。

子序列 是指可以通过删除原数组中的某些（或不删除）元素并保持剩余元素顺序得到的新数组。

交错和 是指一个 从下标 0 开始 的数组中，偶数下标 的元素之和减去 奇数下标 的元素之和。



```
use std::collections::HashSet;

impl Solution {
    pub fn max_product(nums: Vec<i32>, k: i32, limit: i32) -> i32 {
        let total: i32 = nums.iter().sum();
        // |k| 超过所有元素之和，不可能凑出
        if total < k.abs() {
            return -1;
        }

        let n = nums.len();
        let mut ans: i64 = -1;
        let mut vis: HashSet<u64> = HashSet::new();

        // 状态编码：i, s+total(非负), m, odd, empty
        // 位布局：[i:32][s+total:17][m:13][odd:1][empty:1]
        // 注：m 被截断到 limit+1，因此取值范围有限
        let encode = |i: usize, s: i32, m: i64, odd: bool, empty: bool| -> u64 {
            let s_off = (s + total) as u64; // 非负
            (i as u64) << 32
                | s_off << 15
                | (m as u64) << 2
                | ((odd as u64) << 1)
                | (empty as u64)
        };

        // 迭代式 DFS，避免递归开销和借用问题
        // 栈帧：(i, s, m, odd, empty)
        let mut stack: Vec<(usize, i32, i64, bool, bool)> = Vec::with_capacity(1 << 12);
        stack.push((0, 0, 1, false, true));

        while let Some((i, s, m, odd, empty)) = stack.pop() {
            // 剪枝：答案已达到 limit，或已找到答案且当前乘积已超过 limit
            if ans == limit as i64 || (m > limit as i64 && ans >= 0) {
                continue;
            }

            if i == n {
                if !empty && s == k && m <= limit as i64 {
                    ans = ans.max(m);
                }
                continue;
            }

            let mask = encode(i, s, m, odd, empty);
            if !vis.insert(mask) {
                continue;
            }

            let x = nums[i];
            // 压栈顺序不影响正确性；先压"选 x"，再压"不选 x"
            // 选 x：更新交错和与乘积（乘积封顶到 limit+1）
            let new_m = (m * x as i64).min(limit as i64 + 1);
            stack.push((i + 1, s + if odd { -x } else { x }, new_m, !odd, false));
            // 不选 x
            stack.push((i + 1, s, m, odd, empty));
        }

        ans as i32
    }
}
```
