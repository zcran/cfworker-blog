---
title: "leetcode-前缀和13"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 合并石头的最低成本

有 n 堆石头排成一排，第 i 堆中有 stones[i] 块石头。

每次 移动 需要将 连续的 k 堆石头合并为一堆，而这次移动的成本为这 k 堆中石头的总数。

返回把所有石头合并成一堆的最低成本。如果无法合并成一堆，返回 -1 。


```
impl Solution {
    pub fn merge_stones(stones: Vec<i32>, k: i32) -> i32 {
        let n = stones.len();
        let k = k as usize;

        // 检查是否能合并成一堆：每次合并减少 k-1 堆，最终从 n 减到 1
        if (n - 1) % (k - 1) != 0 {
            return -1;
        }

        // 前缀和，用于快速计算任意区间和
        let mut prefix = vec![0; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + stones[i];
        }

        // dp[i][j] 表示将 stones[i..=j] 合并成一堆的最低成本
        // 使用 Vec 存储一维索引，手动映射 (i, j) -> i * n + j
        let mut dp = vec![0; n * n];

        // 区间长度从 k 开始（小于 k 无法合并）
        for len in k..=n {
            for i in 0..=n - len {
                let j = i + len - 1;
                let idx = i * n + j;

                // 将区间分成 k 堆，枚举分割点，步长为 k-1
                let mut min_cost = i32::MAX;
                for mid in (i..j).step_by(k - 1) {
                    let cost = dp[i * n + mid] + dp[(mid + 1) * n + j];
                    if cost < min_cost {
                        min_cost = cost;
                    }
                }
                dp[idx] = min_cost;

                // 如果区间长度能合并成一堆，加上本次合并的成本
                if (len - 1) % (k - 1) == 0 {
                    dp[idx] += prefix[j + 1] - prefix[i];
                }
            }
        }

        dp[n - 1] // 即 dp[0 * n + n - 1]
    }
}
```
