---
title: "leetcode-矩阵30"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 排布二进制网格的最少交换次数

给你一个 n x n 的二进制网格 grid，每一次操作中，你可以选择网格的 相邻两行 进行交换。

一个符合要求的网格需要满足主对角线以上的格子全部都是 0 。

请你返回使网格满足要求的最少操作次数，如果无法使网格符合要求，请你返回 -1 。

主对角线指的是从 (1, 1) 到 (n, n) 的这些格子。


```
impl Solution {
    pub fn min_swaps(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();

        // 记录每一行最右侧 1 所在的列索引，若该行全为 0 则为 -1
        // 条件"主对角线以上全为 0"等价于第 i 行最右侧 1 的位置 <= i
        let mut pos: Vec<i32> = grid
            .iter()
            .map(|row| row.iter().rposition(|&v| v == 1).map_or(-1, |j| j as i32))
            .collect();

        let mut swaps = 0;

        // 贪心：从上到下为每一行找到第一个满足条件的行并冒泡上来
        for i in 0..n {
            let mut found = None;
            for j in i..n {
                if pos[j] <= i as i32 {
                    found = Some(j);
                    break;
                }
            }

            match found {
                Some(j) => {
                    // 将该行通过相邻交换移到位置 i
                    swaps += (j - i) as i32;
                    for k in (i + 1..=j).rev() {
                        pos.swap(k, k - 1);
                    }
                }
                // 找不到满足条件的行，无法完成
                None => return -1,
            }
        }

        swaps
    }
}
```
