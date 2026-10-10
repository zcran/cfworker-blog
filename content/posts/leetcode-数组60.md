---
title: "leetcode-数组60"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最好可到达的塔

给你一个二维整数数组 towers，其中 towers[i] = [xi, yi, qi] 表示第 i 座塔的坐标 (xi, yi) 和质量因子 qi。

另外给你一个整数数组 center = [cx, cy] 表示你的位置，以及一个整数 radius。

如果一座塔与 center 之间的 曼哈顿距离小于或等于 radius，则称该塔是 可到达的。

在所有可到达的塔中：

返回质量因子 最大 的塔的坐标。
如果存在并列的塔，返回坐标 字典序最小 的塔。如果没有塔是可到达的，返回 [-1, -1]。

两点 (xi, yi) 和 (xj, yj) 之间的 曼哈顿距离 为 |xi - xj| + |yi - yj|。
坐标 [xi, yi] 字典序小于 [xj, yj] 是指：xi < xj，或者 xi == xj 且 yi < yj。

|x| 表示 x 的 绝对值。


```
impl Solution {
    pub fn best_tower(towers: Vec<Vec<i32>>, center: Vec<i32>, radius: i32) -> Vec<i32> {
        let (cx, cy) = (center[0], center[1]);

        towers
            .iter()
            .filter(|t| (t[0] - cx).abs() + (t[1] - cy).abs() <= radius)
            // 负质量因子用于取最大值；x、y 保证字典序最小
            .map(|t| (-t[2], t[0], t[1]))
            .min()
            .map_or(vec![-1, -1], |(_, x, y)| vec![x, y])
    }
}
```
