---
title: "leetcode-几何14"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 最小面积矩形 II

给你一个 X-Y 平面上的点数组 points，其中 points[i] = [xi, yi]。

返回由这些点形成的任意矩形的最小面积，矩形的边 不一定 平行于 X 轴和 Y 轴。如果不存在这样的矩形，则返回 0。

答案只需在10-5 的误差范围内即可被视作正确答案。

```
use std::collections::HashSet;

impl Solution {
    pub fn min_area_free_rect(points: Vec<Vec<i32>>) -> f64 {
        let pts: Vec<(i32, i32)> = points.into_iter().map(|p| (p[0], p[1])).collect();
        let n = pts.len();
        if n < 4 {
            return 0.0;
        }
        let set: HashSet<(i32, i32)> = pts.iter().copied().collect();

        // 使用引用避免移动
        let pts_ref = &pts;
        let set_ref = &set;

        let min_area = (0..n)
            .flat_map(|i| {
                let (x1, y1) = pts_ref[i];
                (0..n)
                    .filter(move |&j| j != i)
                    .flat_map(move |j| {
                        let (x2, y2) = pts_ref[j];
                        let dx1 = x2 - x1;
                        let dy1 = y2 - y1;
                        (0..n)
                            .filter(move |&k| k != i && k != j)
                            .filter_map(move |k| {
                                let (x3, y3) = pts_ref[k];
                                let dx2 = x3 - x1;
                                let dy2 = y3 - y1;
                                if dx1 * dx2 + dy1 * dy2 != 0 {
                                    None
                                } else {
                                    let p4 = (x2 + x3 - x1, y2 + y3 - y1);
                                    if set_ref.contains(&p4) {
                                        Some((dx1 * dy2 - dy1 * dx2).abs() as f64)
                                    } else {
                                        None
                                    }
                                }
                            })
                    })
            })
            .fold(f64::INFINITY, |acc, area| acc.min(area));

        if min_area.is_finite() { min_area } else { 0.0 }
    }
}
```
