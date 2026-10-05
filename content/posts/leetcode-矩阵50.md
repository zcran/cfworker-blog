---
title: "leetcode-矩阵50"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵中和能被 K 整除的路径

给你一个下标从 0 开始的 m x n 整数矩阵 grid 和一个整数 k 。你从起点 (0, 0) 出发，每一步只能往 下 或者往 右 ，你想要到达终点 (m - 1, n - 1) 。

请你返回路径和能被 k 整除的路径数目，由于答案可能很大，返回答案对 10^9 + 7 取余 的结果。


```
impl Solution {
    /// 经典「路径数 + 模 k」DP。
    /// dp[i][j][r]：走到 (i,j) 且路径和模 k 余 r 的路径数。
    /// 转移：当前格值为 v，则 dp[i][j][r] = dp[i-1][j][(r-v) mod k] + dp[i][j-1][(r-v) mod k]。
    ///
    /// 优化：
    ///   1. 用滚动两行代替 m+1 层三维数组，空间 O(n*k)。
    ///   2. 下标直接用 0..m、0..n，避免 +1 偏移带来的心智负担。
    ///   3. 提前算好 v = grid[i][j] % k，避免内层重复取模。
    pub fn number_of_paths(grid: Vec<Vec<i32>>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let m = grid.len();
        let n = grid[0].len();
        let k = k as usize;

        // prev[j][r]：上一行第 j 列、模 k 余 r 的路径数
        // cur[j][r] ：当前行第 j 列、模 k 余 r 的路径数
        let mut prev = vec![vec![0i64; k]; n];
        let mut cur = vec![vec![0i64; k]; n];

        for i in 0..m {
            for j in 0..n {
                let v = (grid[i][j] % k as i32) as usize;

                if i == 0 && j == 0 {
                    cur[j][v] = 1;
                    continue;
                }

                // 清空本格的状态（滚动复用 cur）
                cur[j].iter_mut().for_each(|x| *x = 0);

                for r in 0..k {
                    // 需要的前驱余数：从当前余数 r 反推
                    let need = (r + k - v) % k;
                    let mut ways = 0;
                    if i > 0 {
                        ways += prev[j][need];
                    }
                    if j > 0 {
                        ways += cur[j - 1][need];
                    }
                    cur[j][r] = ways % MOD;
                }
            }
            // 本行结束，换到下一行
            std::mem::swap(&mut prev, &mut cur);
        }

        // 换回后 prev 保存的是最后一行，终点为 (m-1, n-1)
        prev[n - 1][0] as i32
    }
}
```
