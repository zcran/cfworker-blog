---
title: "leetcode-几何21"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 圆形靶内的最大飞镖数量

Alice 向一面非常大的墙上掷出 n 支飞镖。给你一个数组 darts ，其中 darts[i] = [xi, yi] 表示 Alice 掷出的第 i 支飞镖落在墙上的位置。

Bob 知道墙上所有 n 支飞镖的位置。他想要往墙上放置一个半径为 r 的圆形靶。使 Alice 掷出的飞镖尽可能多地落在靶上。

给你整数 r ，请返回能够落在 任意 半径为 r 的圆形靶内或靶上的最大飞镖数。

```
impl Solution {
    pub fn num_points(points: Vec<Vec<i32>>, r: i32) -> i32 {
        let pts: Vec<(i32, i32)> = points.iter().map(|p| (p[0], p[1])).collect();
        let n = pts.len();
        let r = r as f64;
        let r_sq = r * r;
        let eps = 1e-9;

        // 统计圆心覆盖点数的闭包
        let count = |cx: f64, cy: f64| -> i32 {
            pts.iter()
                .filter(|&&(x, y)| {
                    let dx = x as f64 - cx;
                    let dy = y as f64 - cy;
                    dx * dx + dy * dy <= r_sq + eps
                })
                .count() as i32
        };

        // 生成所有候选圆心：每个点本身 + 每对点确定的两个圆心
        let centers = (0..n)
            .map(|i| {
                let (x, y) = pts[i];
                (x as f64, y as f64) // 圆心取在点上
            })
            .chain(
                (0..n)
                    .flat_map(|i| (i + 1..n).map(move |j| (i, j)))
                    .filter_map(|(i, j)| {
                        let (x1, y1) = pts[i];
                        let (x2, y2) = pts[j];
                        let dx = (x1 - x2) as f64;
                        let dy = (y1 - y2) as f64;
                        let d_sq = dx * dx + dy * dy;
                        if d_sq > 4.0 * r_sq + eps {
                            return None;
                        }
                        let mid_x = (x1 as f64 + x2 as f64) / 2.0;
                        let mid_y = (y1 as f64 + y2 as f64) / 2.0;
                        let half_d = d_sq.sqrt() / 2.0;
                        let h = (r_sq - half_d * half_d).sqrt();
                        let len = (dx * dx + dy * dy).sqrt();
                        let perp_x = -dy / len;
                        let perp_y = dx / len;
                        Some(vec![
                            (mid_x + perp_x * h, mid_y + perp_y * h),
                            (mid_x - perp_x * h, mid_y - perp_y * h),
                        ])
                    })
                    .flatten(),
            );

        centers
            .map(|(cx, cy)| count(cx, cy))
            .max()
            .unwrap_or(0)
    }
}
```
