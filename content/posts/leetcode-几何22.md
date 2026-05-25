---
title: "leetcode-几何22"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 统计一个圆中点的数目



给你一个数组 points ，其中 points[i] = [xi, yi] ，表示第 i 个点在二维平面上的坐标。多个点可能会有 相同 的坐标。

同时给你一个数组 queries ，其中 queries[j] = [xj, yj, rj] ，表示一个圆心在 (xj, yj) 且半径为 rj 的圆。

对于每一个查询 queries[j] ，计算在第 j 个圆 内 点的数目。如果一个点在圆的 边界上 ，我们同样认为它在圆 内 。

请你返回一个数组 answer ，其中 answer[j]是第 j 个查询的答案。

```
impl Solution {
    /// 统计每个圆内包含的点数
    ///
    /// # 参数
    /// - `points`: 点的坐标列表，每个点由 `[x, y]` 表示
    /// - `queries`: 圆的查询列表，每个圆由 `[cx, cy, r]` 表示
    ///
    /// # 返回
    /// 每个圆内包含的点数，顺序与 `queries` 一致
    pub fn count_points(points: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 使用函数式风格：对每个查询计算满足距离平方 ≤ 半径平方的点数
        queries
            .iter()
            .map(|circle| {
                let (cx, cy, r) = (circle[0], circle[1], circle[2]);
                let r2 = r * r; // 预计算半径平方，避免重复计算
                points
                    .iter()
                    .filter(|point| {
                        let dx = cx - point[0];
                        let dy = cy - point[1];
                        dx * dx + dy * dy <= r2
                    })
                    .count() as i32
            })
            .collect()
    }
}
```
