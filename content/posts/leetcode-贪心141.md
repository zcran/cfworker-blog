---
title: "leetcode-贪心141"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 覆盖所有点的最少矩形数目

给你一个二维整数数组 point ，其中 points[i] = [xi, yi] 表示二维平面内的一个点。同时给你一个整数 w 。你需要用矩形 覆盖所有 点。

每个矩形的左下角在某个点 (x1, 0) 处，且右上角在某个点 (x2, y2) 处，其中 x1 <= x2 且 y2 >= 0 ，同时对于每个矩形都 必须 满足 x2 - x1 <= w 。

如果一个点在矩形内或者在边上，我们说这个点被矩形覆盖了。

请你在确保每个点都 至少 被一个矩形覆盖的前提下，最少 需要多少个矩形。

注意：一个点可以被多个矩形覆盖。


```
impl Solution {
    pub fn min_rectangles_to_cover_points(mut points: Vec<Vec<i32>>, w: i32) -> i32 {
        // 矩形在 y 方向无限制（y2 >= 0，可覆盖所有 y），
        // 因此问题退化为一维：用长度 w 的区间覆盖所有点的 x 坐标。
        // 贪心：按 x 排序，每次从最左未覆盖点开始放一个矩形，
        // 覆盖范围直达 x + w，跳过所有落在其中的点。
        points.sort_unstable_by_key(|p| p[0]);

        let mut count = 0;
        let mut bound = i32::MIN; // 当前矩形右边界
        for p in &points {
            if p[0] > bound {
                bound = p[0] + w;
                count += 1;
            }
        }
        count
    }
}
```
