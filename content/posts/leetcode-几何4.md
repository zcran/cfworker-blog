---
title: "leetcode-几何4"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 完美矩形

给你一个数组 rectangles ，其中 rectangles[i] = [xi, yi, ai, bi] 表示一个坐标轴平行的矩形。这个矩形的左下顶点是 (xi, yi) ，右上顶点是 (ai, bi) 。

如果所有矩形一起精确覆盖了某个矩形区域，则返回 true ；否则，返回 false 。

```
use std::collections::HashMap;

impl Solution {
    pub fn is_rectangle_cover(rectangles: Vec<Vec<i32>>) -> bool {
        // 将矩形转换为 (x, y, a, b) 元组迭代器
        let iter = rectangles.iter().map(|r| (r[0], r[1], r[2], r[3]));

        // 折叠计算：(min_x, min_y, max_x, max_y, 总面积, 顶点计数表)
        let (min_x, min_y, max_x, max_y, total_area, vertex_counts) = iter.fold(
            (i32::MAX, i32::MAX, 0, 0, 0, HashMap::new()),
            |(min_x, min_y, max_x, max_y, area, mut counts), (x, y, a, b)| {
                // 更新大矩形边界
                let new_min_x = min_x.min(x);
                let new_min_y = min_y.min(y);
                let new_max_x = max_x.max(a);
                let new_max_y = max_y.max(b);
                let new_area = area + (a - x) * (b - y);
                // 统计四个顶点
                for &(px, py) in &[(x, y), (x, b), (a, y), (a, b)] {
                    *counts.entry((px, py)).or_insert(0) += 1;
                }
                (new_min_x, new_min_y, new_max_x, new_max_y, new_area, counts)
            },
        );

        // 检查面积
        if (max_x - min_x) * (max_y - min_y) != total_area {
            return false;
        }

        // 检查顶点计数：角点必须为 1，其他必须为偶数
        vertex_counts.into_iter().all(|((x, y), cnt)| {
            let is_corner = (x == min_x || x == max_x) && (y == min_y || y == max_y);
            if is_corner { cnt == 1 } else { cnt % 2 == 0 }
        })
    }
}
```
