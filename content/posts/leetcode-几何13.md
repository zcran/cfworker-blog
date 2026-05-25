---
title: "leetcode-几何13"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 最小面积矩形

给你一个 X-Y 平面上的点数组 points，其中 points[i] = [xi, yi]。

返回由这些点形成的矩形的最小面积，矩形的边与 X 轴和 Y 轴平行。如果不存在这样的矩形，则返回 0。


```
use std::collections::HashSet;

impl Solution {
    pub fn min_area_rect(points: Vec<Vec<i32>>) -> i32 {
        const SCALE: i32 = 40001;

        // 转换为元组
        let pts: Vec<(i32, i32)> = points
            .into_iter()
            .map(|p| (p[0], p[1]))
            .collect();

        // 编码集
        let set: HashSet<i32> = pts
            .iter()
            .map(|&(x, y)| x * SCALE + y)
            .collect();

        let n = pts.len();
        // 生成所有无序对 (i, j)，其中 i < j
        (0..n)
            .flat_map(|i| (i + 1..n).map(move |j| (i, j)))
            .filter_map(|(i, j)| {
                let (x1, y1) = pts[i];
                let (x2, y2) = pts[j];
                if x1 == x2 || y1 == y2 {
                    return None;
                }
                if set.contains(&(x1 * SCALE + y2)) && set.contains(&(x2 * SCALE + y1)) {
                    Some(((x1 - x2) * (y1 - y2)).abs())
                } else {
                    None
                }
            })
            .fold(i32::MAX, |acc, area| acc.min(area))
            .let_it_be(|min| if min == i32::MAX { 0 } else { min })
    }
}

// 辅助 trait 使上述 `let_it_be` 可编译（等价于 `let` 语句的表达式形式）
trait LetItBe<T> {
    fn let_it_be<R>(self, f: impl FnOnce(T) -> R) -> R;
}
impl<T> LetItBe<T> for T {
    fn let_it_be<R>(self, f: impl FnOnce(T) -> R) -> R {
        f(self)
    }
}
```
