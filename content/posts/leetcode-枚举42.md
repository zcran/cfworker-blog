---
title: "leetcode-枚举42"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 黑格子的数目

给你两个整数 m 和 n ，表示一个下标从 0 开始的 m x n 的网格图。

给你一个下标从 0 开始的二维整数矩阵 coordinates ，其中 coordinates[i] = [x, y] 表示坐标为 [x, y] 的格子是 黑色的 ，所有没出现在 coordinates 中的格子都是 白色的。

一个块定义为网格图中 2 x 2 的一个子矩阵。更正式的，对于左上角格子为 [x, y] 的块，其中 0 <= x < m - 1 且 0 <= y < n - 1 ，包含坐标为 [x, y] ，[x + 1, y] ，[x, y + 1] 和 [x + 1, y + 1] 的格子。

请你返回一个下标从 0 开始长度为 5 的整数数组 arr ，arr[i] 表示恰好包含 i 个 黑色 格子的块的数目。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_black_blocks(m: i32, n: i32, coordinates: Vec<Vec<i32>>) -> Vec<i64> {
        let m = m as usize;
        let n = n as usize;
        let total_blocks = (m - 1) * (n - 1);

        // 统计每个 2x2 块中黑格子的数量
        // 键：块左上角坐标 (x, y)，值：黑格子数
        let mut block_count: HashMap<(usize, usize), i32> = HashMap::new();

        // 对于每个黑格子，它会影响以它为右下角、左下角、右上角、左上角的 4 个块
        for coord in coordinates {
            let x = coord[0] as usize;
            let y = coord[1] as usize;

            // 检查黑格子可能作为左上角的 4 个块的左上角坐标
            let candidates = [
                (x, y),           // 黑格子作为块的左上角
                (x.wrapping_sub(1), y),           // 黑格子作为块的上方
                (x, y.wrapping_sub(1)),           // 黑格子作为块的左侧
                (x.wrapping_sub(1), y.wrapping_sub(1)), // 黑格子作为块的左上角
            ];

            for (bx, by) in candidates {
                // 检查块左上角是否在有效范围内
                if bx < m - 1 && by < n - 1 {
                    *block_count.entry((bx, by)).or_insert(0) += 1;
                }
            }
        }

        // 初始化结果数组
        let mut ans = vec![0; 5];

        // 统计包含 1-4 个黑格子的块
        for &count in block_count.values() {
            if count <= 4 {
                ans[count as usize] += 1;
            }
        }

        // 包含 0 个黑格子的块 = 总块数 - 包含黑格子的块数
        let blocks_with_black: i64 = ans.iter().skip(1).sum();
        ans[0] = total_blocks as i64 - blocks_with_black;

        ans
    }
}
```
