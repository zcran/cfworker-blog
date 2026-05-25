---
title: "leetcode-几何20"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 圆和矩形是否有重叠

给你一个以 (radius, xCenter, yCenter) 表示的圆和一个与坐标轴平行的矩形 (x1, y1, x2, y2) ，其中 (x1, y1) 是矩形左下角的坐标，而 (x2, y2) 是右上角的坐标。

如果圆和矩形有重叠的部分，请你返回 true ，否则返回 false 。

换句话说，请你检测是否 存在 点 (xi, yi) ，它既在圆上也在矩形上（两者都包括点落在边界上的情况）。

```
impl Solution {
    pub fn check_overlap(radius: i32, x_center: i32, y_center: i32, x1: i32, y1: i32, x2: i32, y2: i32) -> bool {
        // 整理矩形边界并打包为元组
        let rect = (x1.min(x2), x1.max(x2), y1.min(y2), y1.max(y2));
        // 分别计算 x 和 y 方向上的最近距离平方和
        let dist_sq = [(x_center, rect.0, rect.1), (y_center, rect.2, rect.3)]
            .into_iter()
            .map(|(c, lo, hi)| (c.clamp(lo, hi) - c).pow(2))
            .sum::<i32>();
        dist_sq <= radius * radius
    }
}
```
