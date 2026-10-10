---
title: "leetcode-动态规划31"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 出界的路径数

给你一个大小为 m x n 的网格和一个球。球的起始坐标为 [startRow, startColumn] 。你可以将球移到在四个方向上相邻的单元格内（可以穿过网格边界到达网格之外）。你 最多 可以移动 maxMove 次球。

给你五个整数 m、n、maxMove、startRow 以及 startColumn ，找出并返回可以将球移出边界的路径数量。因为答案可能非常大，返回对 10^9 + 7 取余 后的结果。


```
const MOD: i32 = 1_000_000_007;

impl Solution {
    pub fn find_paths(m: i32, n: i32, max_move: i32, start_row: i32, start_column: i32) -> i32 {
        let (m, n, max_move) = (m as usize, n as usize, max_move as usize);

        // 用两个二维数组滚动：cur 表示移动 i 次后每个格子的路径数，
        // next 表示移动 i+1 次后的路径数，省去 dp 的第三维。
        let mut cur = vec![vec![0i32; n]; m];
        cur[start_row as usize][start_column as usize] = 1;

        let mut total = 0;
        // 四方向偏移
        let dirs = [(0i32, 1i32), (0, -1), (1, 0), (-1, 0)];

        for _ in 0..max_move {
            let mut next = vec![vec![0i32; n]; m];

            for i in 0..m {
                for j in 0..n {
                    let paths = cur[i][j];
                    if paths == 0 {
                        continue;
                    }
                    for (dx, dy) in dirs {
                        let ni = i as i32 + dx;
                        let nj = j as i32 + dy;
                        if ni < 0 || ni >= m as i32 || nj < 0 || nj >= n as i32 {
                            // 移动到网格外，累计答案
                            total = (total + paths) % MOD;
                        } else {
                            // 移动到相邻格子，累加到 next
                            next[ni as usize][nj as usize] =
                                (next[ni as usize][nj as usize] + paths) % MOD;
                        }
                    }
                }
            }

            cur = next;
        }

        total
    }
}
```
