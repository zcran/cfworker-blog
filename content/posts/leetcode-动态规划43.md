---
title: "leetcode-动态规划43"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最大加号标志

在一个 n x n 的矩阵 grid 中，除了在数组 mines 中给出的元素为 0，其他每个元素都为 1。mines[i] = [xi, yi]表示 grid[xi][yi] == 0

返回  grid 中包含 1 的最大的 轴对齐 加号标志的阶数 。如果未找到加号标志，则返回 0 。

一个 k 阶由 1 组成的 “轴对称”加号标志 具有中心网格 grid[r][c] == 1 ，以及4个从中心向上、向下、向左、向右延伸，长度为 k-1，由 1 组成的臂。注意，只有加号标志的所有网格要求为 1 ，别的网格可能为 0 也可能为 1 。


```
impl Solution {
    /// 764. 最大加号标志
    ///
    /// 对每个格子，计算其四个方向（上、下、左、右）连续 1 的长度，
    /// 取四者最小值即为以该格子为中心的加号阶数，全局取最大即可。
    /// 通过四趟扫描（每趟只用一个方向）复用同一张表，避免额外空间。
    pub fn order_of_largest_plus_sign(n: i32, mines: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;
        // dp[i][j]：以 (i,j) 为中心的加号最大可能阶数，初始为 n（上界）
        // 遇到地雷置 0
        let mut dp = vec![vec![n as i32; n]; n];
        for m in &mines {
            dp[m[0] as usize][m[1] as usize] = 0;
        }

        // 左右扫描：对每一行，分别从左、从右累计连续 1 的长度并取 min
        for i in 0..n {
            let mut len = 0;
            for j in 0..n {
                len = if dp[i][j] == 0 { 0 } else { len + 1 };
                dp[i][j] = dp[i][j].min(len);
            }
            len = 0;
            for j in (0..n).rev() {
                len = if dp[i][j] == 0 { 0 } else { len + 1 };
                dp[i][j] = dp[i][j].min(len);
            }
        }

        // 上下扫描：对每一列，分别从上、从下累计连续 1 的长度并取 min
        for j in 0..n {
            let mut len = 0;
            for i in 0..n {
                len = if dp[i][j] == 0 { 0 } else { len + 1 };
                dp[i][j] = dp[i][j].min(len);
            }
            len = 0;
            for i in (0..n).rev() {
                len = if dp[i][j] == 0 { 0 } else { len + 1 };
                dp[i][j] = dp[i][j].min(len);
            }
        }

        // dp[i][j] 已是该格四方向最小连续长度，即最大加号阶数
        dp.iter().flatten().copied().max().unwrap_or(0)
    }
}
```
