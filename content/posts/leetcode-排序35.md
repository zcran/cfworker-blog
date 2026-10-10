---
title: "leetcode-排序35"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 切棍子的最小成本

有一根长度为 n 个单位的木棍，棍上从 0 到 n 标记了若干位置。例如，长度为 6 的棍子可以标记如下：

 0       1       2       3       4       5       6

 |       |       |       |       |       |       |

\--------------------------------------------------

给你一个整数数组 cuts ，其中 cuts[i] 表示你需要将棍子切开的位置。

你可以按顺序完成切割，也可以根据需要更改切割的顺序。

每次切割的成本都是当前要切割的棍子的长度，切棍子的总成本是历次切割成本的总和。对棍子进行切割将会把一根木棍分成两根较小的木棍（这两根木棍的长度和就是切割前木棍的长度）。请参阅第一个示例以获得更直观的解释。

返回切棍子的 最小总成本 。


```
use std::cmp::min;

impl Solution {
    /// 切棍子的最小总成本
    ///
    /// 思路：把切割位置排序后加入两端 0 和 n，问题转化为区间 DP。
    /// f[i][j] 表示切完区间 (cuts[i], cuts[j]) 内部所有切口的最小成本。
    /// 枚举最后一次切点 k：f[i][j] = min{ f[i][k] + f[k][j] } + (cuts[j] - cuts[i])。
    /// 当前棍长为 cuts[j] - cuts[i]，是本次切割的成本，最后统一加上。
    /// 时间 O(m³)，额外空间 O(m²)，m = cuts.len() + 2。
    pub fn min_cost(n: i32, mut cuts: Vec<i32>) -> i32 {
        cuts.push(0);
        cuts.push(n);
        cuts.sort_unstable();
        let m = cuts.len();

        // f[i][j]：区间 (cuts[i], cuts[j]) 内的最小切割成本
        let mut f = vec![vec![0i32; m]; m];

        // 逆序枚举 i、正序枚举 j，保证子区间先于当前区间被计算
        for i in (0..m).rev() {
            for j in i + 2..m {
                let mut best = i32::MAX;
                for k in i + 1..j {
                    best = min(best, f[i][k] + f[k][j]);
                }
                // 加上本次切割的成本（当前棍长）
                f[i][j] = best + cuts[j] - cuts[i];
            }
        }

        f[0][m - 1]
    }
}
```
