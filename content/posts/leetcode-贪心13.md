---
title: "leetcode-贪心13"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 用最少数量的箭引爆气球

有一些球形气球贴在一堵用 XY 平面表示的墙面上。墙面上的气球记录在整数数组 points ，其中points[i] = [xstart, xend] 表示水平直径在 xstart 和 xend之间的气球。你不知道气球的确切 y 坐标。

一支弓箭可以沿着 x 轴从不同点 完全垂直 地射出。在坐标 x 处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，则该气球会被 引爆 。可以射出的弓箭的数量 没有限制 。 弓箭一旦被射出之后，可以无限地前进。

给你一个数组 points ，返回引爆所有气球所必须射出的 最小 弓箭数 。


```
impl Solution {
    pub fn find_min_arrow_shots(mut points: Vec<Vec<i32>>) -> i32 {
        if points.is_empty() {
            return 0;
        }

        // 按右端点升序排序：每次尽量把箭射在当前最靠左的右端点上，能覆盖更多区间
        points.sort_unstable_by_key(|p| p[1]);

        // arrows: 已射箭数；end: 当前这支箭的位置（即某个区间的右端点）
        let mut arrows = 1;
        let mut end = points[0][1];

        for p in &points[1..] {
            // 当前气球左端点在箭的右侧，说明这支箭射不到，需要再射一箭
            if p[0] > end {
                arrows += 1;
                end = p[1];
            }
        }

        arrows
    }
}
```
