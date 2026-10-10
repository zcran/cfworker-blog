---
title: "leetcode-动态规划145"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 粉刷房子 IV

给你一个 偶数 整数 n，表示沿直线排列的房屋数量，以及一个大小为 n x 3 的二维数组 cost，其中 cost[i][j] 表示将第 i 个房屋涂成颜色 j + 1 的成本。

如果房屋满足以下条件，则认为它们看起来 漂亮：

不存在 两个 涂成相同颜色的相邻房屋。

距离行两端 等距 的房屋不能涂成相同的颜色。例如，如果 n = 6，则位置 (0, 5)、(1, 4) 和 (2, 3) 的房屋被认为是等距的。

返回使房屋看起来 漂亮 的 最低 涂色成本。


```
impl Solution {
    pub fn min_cost(n: i32, cost: Vec<Vec<i32>>) -> i64 {
        let n = n as usize;
        let half = n / 2;
        const INF: i64 = i64::MAX / 2;

        // dp[j][k]：当前处理到第 i 对（从外向内），
        // 左侧房屋涂颜色 j、右侧对称房屋涂颜色 k 时的最小成本
        let mut dp = [[0i64; 3]; 3];

        for i in 0..half {
            let left = &cost[i];
            let right = &cost[n - 1 - i];

            let mut next = [[INF; 3]; 3];

            // pre_j / pre_k：上一对（更外层）左右两侧的颜色
            for pre_j in 0..3 {
                for pre_k in 0..3 {
                    let base = dp[pre_j][pre_k];

                    // j：当前左侧颜色，需与 pre_j 不同
                    for j in 0..3 {
                        if j == pre_j {
                            continue;
                        }
                        // k：当前右侧颜色，需与 pre_k 不同，且与 j 不同（等距房屋不能同色）
                        for k in 0..3 {
                            if k != pre_k && k != j {
                                let c = base + left[j] as i64 + right[k] as i64;
                                if c < next[j][k] {
                                    next[j][k] = c;
                                }
                            }
                        }
                    }
                }
            }

            dp = next;
        }

        // 所有初始颜色组合取最小值
        dp.iter()
            .flat_map(|row| row.iter())
            .copied()
            .min()
            .unwrap()
    }
}
```
