---
title: "leetcode-矩阵80"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---

## 网格中保持一致的最大列数


给你一个大小为 m x n 的二维整数数组 grid，和一个整数 limit。

你可以从网格中移除零个或多个列，但必须至少保留一列。剩余列的 相对 顺序必须保持不变。

如果对于每一行 i，以及每一对相邻的剩余列 a 和 b（其中 a < b），都满足 |grid[i][b] - grid[i][a]| <= limit，则称该网格是 一致的。

返回网格成为 一致的 所能保留的 最大 列数。



```
impl Solution {
    pub fn max_consistent_columns(grid: Vec<Vec<i32>>, limit: i32) -> i32 {
        let n = grid[0].len();
        // f[i]：以第 i 列作为最后一列时，能保留的最大列数
        let mut f = vec![0i32; n];
        let mut ans = 0;

        for i in 0..n {
            // 枚举上一个保留的列 j
            for j in (0..i).rev() {
                // 剪枝：如果 f[j] 不比当前 f[i] 大，则无法产生更优结果
                if f[j] <= f[i] {
                    continue;
                }

                // 检查第 i 列和第 j 列是否满足一致性
                let ok = grid.iter().all(|row| (row[i] - row[j]).abs() <= limit);

                if ok {
                    f[i] = f[j];
                }
            }
            // 加上当前列自身
            f[i] += 1;
            ans = ans.max(f[i]);
        }

        ans
    }
}
```
