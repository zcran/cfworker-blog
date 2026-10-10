---
title: "leetcode-动态规划132"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计逆序对的数目

给你一个整数 n 和一个二维数组 requirements ，其中 requirements[i] = [endi, cnti] 表示这个要求中的末尾下标和 逆序对 的数目。

整数数组 nums 中一个下标对 (i, j) 如果满足以下条件，那么它们被称为一个 逆序对 ：

i < j 且 nums[i] > nums[j]
请你返回 [0, 1, 2, ..., n - 1] 的 排列 perm 的数目，满足对 所有 的 requirements[i] 都满足 perm[0..endi] 中恰好有 cnti 个逆序对。

由于答案可能会很大，将它对 10^9 + 7 取余 后返回。


```
use std::collections::HashMap;

const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn number_of_permutations(n: i32, requirements: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;

        // 把要求转换成数组，req[end] = Some(cnt) 表示 perm[0..=end] 必须恰好有 cnt 个逆序对
        let mut req = vec![None; n];
        let mut max_cnt = 0;
        for r in &requirements {
            let end = r[0] as usize;
            let cnt = r[1];
            req[end] = Some(cnt);
            max_cnt = max_cnt.max(cnt);
        }

        // 起点必须满足 perm[0..=0] 有 0 个逆序对
        if req[0].is_some_and(|c| c != 0) {
            return 0;
        }
        req[0] = Some(0);

        // dp[cnt] 表示当前 end 下，逆序对数为 cnt 的方案数
        // 使用滚动数组，避免 O(n * max_cnt) 的二维内存
        let width = (max_cnt + 1) as usize;
        let mut dp = vec![0i64; width];
        dp[0] = 1;

        // 前缀和辅助数组，用于 O(1) 计算区间和
        let mut prefix = vec![0i64; width + 1];

        for end in 0..n {
            if end > 0 {
                // 计算上一轮的 prefix 和，用于滑动窗口转移
                prefix[0] = 0;
                for c in 0..width {
                    prefix[c + 1] = (prefix[c] + dp[c]) % MOD;
                }

                let mut next = vec![0i64; width];

                // 当把值 end 插入到长度为 end 的排列末尾时，
                // 可以新增 0..=end 个逆序对。
                // next[c] = sum(dp[c - k])，其中 k ∈ [0, min(end, c)]
                // 即 next[c] = prefix[c + 1] - prefix[max(0, c - end)]
                for c in 0..width {
                    let lo = c.saturating_sub(end);
                    next[c] = (prefix[c + 1] - prefix[lo] + MOD) % MOD;
                }

                dp = next;
            }

            // 如果当前位置有硬性要求，则只保留满足要求的状态
            if let Some(required) = req[end] {
                let required = required as usize;
                if required >= width {
                    return 0;
                }
                for c in 0..width {
                    if c != required {
                        dp[c] = 0;
                    }
                }
            }
        }

        dp.iter().sum::<i64>().wrapping_rem(MOD) as i32
    }
}
```
