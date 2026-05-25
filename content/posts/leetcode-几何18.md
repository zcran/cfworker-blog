---
title: "leetcode-几何18"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 缀点成线

给定一个整数数组 coordinates ，其中 coordinates[i] = [x, y] ， [x, y] 表示横坐标为 x、纵坐标为 y 的点。请你来判断，这些点是否在该坐标系中属于同一条直线上。

```
impl Solution {
    pub fn check_straight_line(coordinates: Vec<Vec<i32>>) -> bool {
        // 取前两点坐标
        let (x1, y1) = (coordinates[0][0], coordinates[0][1]);
        let (x2, y2) = (coordinates[1][0], coordinates[1][1]);
        let dx = x2 - x1;
        let dy = y2 - y1;

        // 检查所有剩余点是否满足 (x - x1)*dy == dx*(y - y1)
        coordinates.into_iter()
            .skip(2)
            .all(|p| (p[0] - x1) * dy == dx * (p[1] - y1))
    }
}
```
