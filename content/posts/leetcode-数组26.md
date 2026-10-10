---
title: "leetcode-数组26"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 找到最近的有相同 X 或 Y 坐标的点

给你两个整数 x 和 y ，表示你在一个笛卡尔坐标系下的 (x, y) 处。同时，在同一个坐标系下给你一个数组 points ，其中 points[i] = [ai, bi] 表示在 (ai, bi) 处有一个点。当一个点与你所在的位置有相同的 x 坐标或者相同的 y 坐标时，我们称这个点是 有效的 。

请返回距离你当前位置 曼哈顿距离 最近的 有效 点的下标（下标从 0 开始）。如果有多个最近的有效点，请返回下标 最小 的一个。如果没有有效点，请返回 -1 。

两个点 (x1, y1) 和 (x2, y2) 之间的 曼哈顿距离 为 abs(x1 - x2) + abs(y1 - y2) 。


```
impl Solution {
    /// 找与 (x, y) 共享 x 或 y 坐标的有效点中，曼哈顿距离最近的下标。
    ///
    /// 遍历所有点，筛出有效点（x 坐标或 y 坐标相同），
    /// 计算曼哈顿距离并维护最小值。由于按顺序遍历且仅在严格更小时更新，
    /// 相等距离自动保留较小下标。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn nearest_valid_point(x: i32, y: i32, points: Vec<Vec<i32>>) -> i32 {
        let mut best_idx = -1;
        let mut best_dist = i32::MAX;

        for (i, p) in points.iter().enumerate() {
            let (px, py) = (p[0], p[1]);
            if px == x || py == y {
                let dist = (x - px).abs() + (y - py).abs();
                if dist < best_dist {
                    best_dist = dist;
                    best_idx = i as i32;
                }
            }
        }

        best_idx
    }
}
```
