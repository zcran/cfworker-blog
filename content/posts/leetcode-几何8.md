---
title: "leetcode-几何8"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 最大三角形面积

给你一个由 X-Y 平面上的点组成的数组 points ，其中 points[i] = [xi, yi] 。从其中取任意三个不同的点组成三角形，返回能组成的最大三角形的面积。与真实值误差在 10-5 内的答案将会视为正确答案。

```
impl Solution {
    pub fn largest_triangle_area(points: Vec<Vec<i32>>) -> f64 {
        // 转换为 (i64, i64) 元组，避免后续重复转换和溢出
        let pts: Vec<(i64, i64)> = points
            .iter()
            .map(|p| (p[0] as i64, p[1] as i64))
            .collect();
        let n = pts.len();

        // 生成所有索引三元组 (i, j, k) 且 i < j < k
        (0..n)
            .flat_map(|i| (i + 1..n).flat_map(move |j| (j + 1..n).map(move |k| (i, j, k))))
            .map(|(i, j, k)| {
                let (x1, y1) = pts[i];
                let (x2, y2) = pts[j];
                let (x3, y3) = pts[k];
                let cross = ((x2 - x1) * (y3 - y1) - (x3 - x1) * (y2 - y1)).abs();
                cross as f64 * 0.5
            })
            .fold(0.0, f64::max) // 等价于 .max().unwrap_or(0.0)
    }
}
```
