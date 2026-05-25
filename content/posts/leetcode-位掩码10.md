---
title: "leetcode-位掩码10"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 每个人戴不同帽子的方案数

总共有 n 个人和 40 种不同的帽子，帽子编号从 1 到 40 。

给你一个整数列表的列表 hats ，其中 hats[i] 是第 i 个人所有喜欢帽子的列表。

请你给每个人安排一顶他喜欢的帽子，确保每个人戴的帽子跟别人都不一样，并返回方案数。

由于答案可能很大，请返回它对 10^9 + 7 取余后的结果。

```
impl Solution {
    pub fn number_ways(hats: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = hats.len();

        // 找到最大帽子编号
        let max_hat_id = hats.iter().flatten().max().copied().unwrap_or(0) as usize;

        // 构建帽子 -> 喜欢的人的映射（0-indexed）
        let mut hat_to_person = vec![vec![]; max_hat_id + 1];
        for (i, person_hats) in hats.iter().enumerate() {
            for &h in person_hats {
                let h = h as usize;
                hat_to_person[h].push(i);
            }
        }

        let full_mask = (1 << n) - 1;
        // 滚动 DP：dp[mask] 表示考虑当前帽子之前的方案数
        let mut dp = vec![0i64; 1 << n];
        dp[0] = 1; // 初始状态：无人戴帽子

        // 遍历每一顶帽子（按编号从小到大）
        for i in 1..=max_hat_id {
            let mut new_dp = dp.clone(); // 先继承不选这顶帽子的结果
            for mask in 0..=full_mask {
                // 对于每个已分配集合 mask，尝试给当前帽子分配一个人
                for &p in &hat_to_person[i] {
                    if (mask >> p) & 1 == 0 { continue; } // 这个人已经戴了别的帽子？不，这里mask含p表示这个人已经分配了帽子，但我们要的是当前帽子给一个人，所以应该是mask要包含p？注意原转移：如果mask包含j，则f[i][mask] += f[i-1][mask ^ (1<<j)]。即当前帽子分配给j，那么之前的状态是除去j的mask。所以新dp[mask] = dp[mask] + sum_{j in hat_to_person[i] and mask contains j} dp[mask ^ (1<<j)]。
                    // 因此我们遍历mask，如果mask包含p，则new_dp[mask] += dp[mask ^ (1<<p)]
                    let prev_mask = mask ^ (1 << p);
                    new_dp[mask] = (new_dp[mask] + dp[prev_mask]) % MOD;
                }
            }
            dp = new_dp;
        }

        dp[full_mask] as i32
    }
}
```
