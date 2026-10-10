---
title: "leetcode-排序41"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 两点之间不包含任何点的最宽垂直区域

给你 n 个二维平面上的点 points ，其中 points[i] = [xi, yi] ，请你返回两点之间内部不包含任何点的 最宽垂直区域 的宽度。

垂直区域 的定义是固定宽度，而 y 轴上无限延伸的一块区域（也就是高度为无穷大）。 最宽垂直区域 为宽度最大的一个垂直区域。

请注意，垂直区域 边上 的点 不在 区域内。


```
impl Solution {
    /// 返回两点之间内部不包含任何点的最宽垂直区域的宽度。
    ///
    /// 思路：垂直区域不包含任何点，等价于区域宽度由相邻两个点的 x 坐标差决定。
    /// 因此按 x 坐标排序后，取相邻 x 差的最大值即可。
    pub fn max_width_of_vertical_area(mut points: Vec<Vec<i32>>) -> i32 {
        // 按 x 坐标升序排序，便于只比较相邻点
        points.sort_unstable_by_key(|p| p[0]);

        // 扫描相邻点的 x 差值，维护最大值
        points
            .windows(2)
            .map(|w| w[1][0] - w[0][0])
            .max()
            .unwrap_or(0)
    }
}
```
