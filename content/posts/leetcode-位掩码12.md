---
title: "leetcode-位掩码12"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 连通两组点的最小成本

给你两组点，其中第一组中有 size1 个点，第二组中有 size2 个点，且 size1 >= size2 。

任意两点间的连接成本 cost 由大小为 size1 x size2 矩阵给出，其中 cost[i][j] 是第一组中的点 i 和第二组中的点 j 的连接成本。如果两个组中的每个点都与另一组中的一个或多个点连接，则称这两组点是连通的。换言之，第一组中的每个点必须至少与第二组中的一个点连接，且第二组中的每个点必须至少与第一组中的一个点连接。

返回连通两组点所需的最小成本。

```
use std::collections::HashMap;

impl Solution {
    pub fn connect_two_groups(cost: Vec<Vec<i32>>) -> i32 {
        let n = cost.len();
        let m = cost[0].len();

        // 预计算每个第二组点的最小连接代价
        let min_to_second: Vec<i32> = (0..m)
            .map(|j| cost.iter().map(|row| row[j]).min().unwrap())
            .collect();

        // 记忆化： (i, mask) -> 最小代价
        let mut memo = HashMap::new();

        fn dfs(
            i: usize,
            mask: usize,
            n: usize,
            m: usize,
            cost: &[Vec<i32>],
            min_to_second: &[i32],
            memo: &mut HashMap<(usize, usize), i32>,
        ) -> i32 {
            if i == n {
                // 第一组点已用完，剩下未连接的第二组点只能用最小代价补齐
                let mut remaining = mask;
                let mut sum = 0;
                while remaining != 0 {
                    let j = remaining.trailing_zeros() as usize;
                    sum += min_to_second[j];
                    remaining &= remaining - 1;
                }
                return sum;
            }
            if let Some(&res) = memo.get(&(i, mask)) {
                return res;
            }

            let mut best = i32::MAX;
            // 当前第一组点 i 必须连接至少一个第二组点
            for k in 0..m {
                let new_mask = mask & !(1 << k); // 连接后，k 被覆盖，可以从 mask 中移除
                let cand = cost[i][k] + dfs(i + 1, new_mask, n, m, cost, min_to_second, memo);
                best = best.min(cand);
            }
            memo.insert((i, mask), best);
            best
        }

        dfs(0, (1 << m) - 1, n, m, &cost, &min_to_second, &mut memo)
    }
}
```
