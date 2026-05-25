---
title: "leetcode-位掩码2"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 优美的排列

假设有从 1 到 n 的 n 个整数。用这些整数构造一个数组 perm（下标从 1 开始），只要满足下述条件 之一 ，该数组就是一个 优美的排列 ：

· perm[i] 能够被 i 整除
· i 能够被 perm[i] 整除

给你一个整数 n ，返回可以构造的 优美排列 的 数量 。

```
impl Solution {
    pub fn count_arrangement(n: i32) -> i32 {
        let n = n as usize;
        let full_mask = (1 << n) - 1;                 // 所有数字都用过的状态

        // 预计算每个位置（1-indexed）能放哪些数字
        let valid: Vec<Vec<usize>> = (1..=n)
            .map(|pos| {
                (1..=n)
                    .filter(|&num| pos % num == 0 || num % pos == 0)
                    .collect()
            })
            .collect();

        // memo[state] = 从该状态出发的排列数，-1 表示未计算
        let mut memo = vec![-1; 1 << n];

        fn dfs(state: usize, full: usize, memo: &mut Vec<i32>, valid: &[Vec<usize>]) -> i32 {
            if state == full {
                return 1;               // 所有数字已放置
            }
            if memo[state] != -1 {
                return memo[state];
            }

            let pos = state.count_ones() as usize; // 下一个要放置的位置（0‑based）
            let mut count = 0;

            for &num in &valid[pos] {               // 只尝试合法的数字
                let bit = 1 << (num - 1);
                if state & bit == 0 {               // 该数字还未使用
                    count += dfs(state | bit, full, memo, valid);
                }
            }

            memo[state] = count;
            count
        }

        dfs(0, full_mask, &mut memo, &valid)
    }
}
```
