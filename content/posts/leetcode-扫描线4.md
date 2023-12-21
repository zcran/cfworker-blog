---
title: "Leetcode 扫描线4"
date: 2023-12-19T15:44:18+08:00
tags: ["leetcode", "扫描线"]
draft: false
---

## 完美矩形

给你一个数组 rectangles ，其中 rectangles[i] = [x⌄i, y⌄i, a⌄i, b⌄i] 表示一个坐标轴平行的矩形。这个矩形的左下顶点是 (x⌄i, y⌄i) ，右上顶点是 (a⌄i, b⌄i) 。

如果所有矩形一起精确覆盖了某个矩形区域，则返回 true ；否则，返回 false 。

```
 // 格林公式
use std::collections::HashSet;

impl Solution {
    pub fn is_rectangle_cover(rectangles: Vec<Vec<i32>>) -> bool {
        let (x1, y1, x2, y2, a, s) = rectangles
            .iter()
            .fold((i32::MAX, i32::MAX, i32::MIN, i32::MIN, 0, HashSet::new()), |(x1, y1, x2, y2, a, mut s), rect| {
                for p in [(rect[0], rect[1]), (rect[2], rect[1]), (rect[0], rect[3]), (rect[2], rect[3])] {
                    if !s.remove(&p) {
                        s.insert(p);
                    }
                }

                (x1.min(rect[0]), y1.min(rect[1]), x2.max(rect[2]), y2.max(rect[3]), a + (rect[2] - rect[0]) * (rect[3] - rect[1]), s)
            });

        (x2 - x1) * (y2 - y1) == a && s == HashSet::from([(x1, y1), (x1, y2), (x2, y1), (x2, y2)])   
    }
}
```