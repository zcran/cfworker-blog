---
title: "leetcode-几何7"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 有效的正方形

给定2D空间中四个点的坐标 p1, p2, p3 和 p4，如果这四个点构成一个正方形，则返回 true 。

点的坐标 pi 表示为 [xi, yi] 。 输入没有任何顺序 。

一个 有效的正方形 有四条等边和四个等角(90度角)。

```
impl Solution {
    /// 判断四个点是否能构成正方形（点顺序任意）
    pub fn valid_square(p1: Vec<i32>, p2: Vec<i32>, p3: Vec<i32>, p4: Vec<i32>) -> bool {
        // 存储点的引用，避免移动所有权（引用是 Copy 类型）
        let pts = [&p1, &p2, &p3, &p4];

        // 生成所有无序点对的距离平方
        let mut dists: Vec<i32> = (0..4)
            .flat_map(|i| (i + 1..4).map(move |j| {
                let dx = pts[i][0] - pts[j][0];
                let dy = pts[i][1] - pts[j][1];
                dx * dx + dy * dy
            }))
            .collect();

        dists.sort_unstable();

        // 正方形条件：4 条边相等且 > 0，2 条对角线相等且 = 2 * 边长²
        match dists.as_slice() {
            [a, b, c, d, e, f] if *a > 0 && a == b && b == c && c == d && e == f && *e == 2 * a => true,
            _ => false,
        }
    }
}
```
