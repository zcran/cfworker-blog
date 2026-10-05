---
title: "leetcode-矩阵81"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 两个不重叠子正方形的最大面积

给你一个大小为 m × n 的二维整数矩阵 mat，其中：

mat[r][c] == 1 表示位于行 r 和列 c 的单元格是可用的。
mat[r][c] == 0 表示它不可用。

你的任务是找到满足以下条件的 两个子矩阵 ：

这两个子矩阵都必须是边长为 k 的正方形。
这两个子矩阵不能共享任何单元格。
每个子矩阵只能覆盖 mat[r][c] == 1 的单元格。

返回单个正方形的最大可能面积。如果无法选择两个这样的正方形，则返回 0。

一个 子矩阵 (x1, y1, x2, y2) 包括所有满足 x1 <= x <= x2 且 y1 <= y <= y2 的单元格 mat[x][y] 。


```
impl Solution {
    pub fn max_area(mat: Vec<Vec<i32>>) -> i32 {
        let m = mat.len();
        let n = mat[0].len();

        // pref[i+1][j+1]：以 (0,0) 到 (i,j) 为范围的二维前缀和
        // 同时用 dp[i+1][j+1] 表示以 (i,j) 为右下角的最大全 1 正方形边长
        let mut dp = vec![vec![0i32; n + 1]; m + 1];

        // pos[side] 存储所有“以 (i,j) 为右下角、最大全 1 正方形边长恰好为 side+1”的右下角位置
        // 编码方式：i * n + j
        let mut pos: Vec<Vec<usize>> = Vec::new();

        for (i, row) in mat.iter().enumerate() {
            for (j, &val) in row.iter().enumerate() {
                if val != 0 {
                    // 以 (i,j) 为右下角的最大全 1 正方形边长
                    let side = dp[i][j].min(dp[i + 1][j]).min(dp[i][j + 1]) + 1;
                    dp[i + 1][j + 1] = side;

                    // 按边长分组
                    let idx = side as usize - 1;
                    if idx >= pos.len() {
                        pos.push(Vec::new());
                    }
                    pos[idx].push(i * n + j);
                }
            }
        }

        // 没有任何可用正方形
        if pos.is_empty() {
            return 0;
        }
        // 只有边长 1 且数量不足 2 个
        if pos.len() == 1 && pos[0].len() < 2 {
            return 0;
        }

        // 从大到小枚举边长，找到第一对不重叠的正方形即为答案
        for side in (1..=pos.len()).rev() {
            let arr = &pos[side - 1];
            if arr.len() < 2 {
                continue;
            }

            // 检查任意两个正方形是否不重叠
            for left in 0..arr.len() - 1 {
                let x1 = arr[left] / n;
                let y1 = arr[left] % n;

                for right in (left + 1..arr.len()).rev() {
                    let x2 = arr[right] / n;
                    let y2 = arr[right] % n;

                    // 两个正方形不重叠的充要条件：行区间或列区间不相交
                    let no_overlap = x1 + side <= x2
                        || x2 + side <= x1
                        || y1 + side <= y2
                        || y2 + side <= y1;

                    if no_overlap {
                        return (side * side) as i32;
                    }
                }
            }
        }

        0
    }
}
```
