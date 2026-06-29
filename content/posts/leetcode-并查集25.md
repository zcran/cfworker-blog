---
title: "leetcode-并查集25"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 统计参与通信的服务器

这里有一幅服务器分布图，服务器的位置标识在 m * n 的整数矩阵网格 grid 中，1 表示单元格上有服务器，0 表示没有。

如果两台服务器位于同一行或者同一列，我们就认为它们之间可以进行通信。

请你统计并返回能够与至少一台其他服务器进行通信的服务器的数量。




```
impl Solution {
    /// 返回能够与至少一台其他服务器通信的服务器数量。
    /// 通信条件：同一行或同一列有多台服务器。
    /// 统计每行和每列的服务器数量，只有行/列数量 > 1 的服务器才能通信。
    pub fn count_servers(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // 1. 统计每行和每列的服务器数量
        let mut row_count = vec![0; m];
        let mut col_count = vec![0; n];
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] == 1 {
                    row_count[i] += 1;
                    col_count[j] += 1;
                }
            }
        }

        // 2. 统计能通信的服务器
        let mut result = 0;
        for i in 0..m {
            for j in 0..n {
                if grid[i][j] == 1 && (row_count[i] > 1 || col_count[j] > 1) {
                    result += 1;
                }
            }
        }

        result
    }
}
```
