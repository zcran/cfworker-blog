---
title: "leetcode-二分查找74"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最长上升路径的长度

给你一个长度为 n 的二维整数数组 coordinates 和一个整数 k ，其中 0 <= k < n 。

coordinates[i] = [xi, yi] 表示二维平面里一个点 (xi, yi) 。

如果一个点序列 (x1, y1), (x2, y2), (x3, y3), ..., (xm, ym) 满足以下条件，那么我们称它是一个长度为 m 的 上升序列 ：

对于所有满足 1 <= i < m 的 i 都有 xi < xi + 1 且 yi < yi + 1 。
对于所有 1 <= i <= m 的 i 对应的点 (xi, yi) 都在给定的坐标数组里。

请你返回包含坐标 coordinates[k] 的 最长上升路径 的长度。


```
impl Solution {
    pub fn max_path_length(mut coordinates: Vec<Vec<i32>>, k: i32) -> i32 {
        let k = k as usize;
        let (kx, ky) = (coordinates[k][0], coordinates[k][1]);

        // 按 x 升序，x 相同时按 y 降序，保证同一 x 的点不会在同一上升序列中被重复选取
        coordinates.sort_unstable_by(|a, b| a[0].cmp(&b[0]).then(b[1].cmp(&a[1])));

        // 对满足严格小于/大于 (kx, ky) 的点求 y 的最长上升子序列
        let mut g: Vec<i32> = Vec::new();
        for p in &coordinates {
            let (x, y) = (p[0], p[1]);
            if (x < kx && y < ky) || (x > kx && y > ky) {
                match g.binary_search(&y) {
                    Ok(pos) => g[pos] = y,
                    Err(pos) => {
                        if pos == g.len() {
                            g.push(y);
                        } else {
                            g[pos] = y;
                        }
                    }
                }
            }
        }

        g.len() as i32 + 1 // 加上 coordinates[k] 本身
    }
}
```
