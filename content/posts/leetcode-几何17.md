---
title: "leetcode-几何17"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 有效的回旋镖

给定一个数组 points ，其中 points[i] = [xi, yi] 表示 X-Y 平面上的一个点，如果这些点构成一个 回旋镖 则返回 true 。

回旋镖 定义为一组三个点，这些点 各不相同 且 不在一条直线上 。



```
impl Solution {
    pub fn is_boomerang(points: Vec<Vec<i32>>) -> bool {
        // 提取坐标元组（使用 into_iter 消耗 points，但通常 points 不再需要）
        let coords: Vec<(i32, i32)> = points.into_iter()
            .map(|p| (p[0], p[1]))
            .collect();
        // 若 points 仍需保留，可改用 iter() 并克隆，但此处直接消耗更简洁
        match &coords[..] {
            [a, b, c] => (b.0 - a.0) * (c.1 - a.1) != (b.1 - a.1) * (c.0 - a.0),
            _ => false, // 输入保证长度为3，但为匹配完整性添加
        }
    }
}
```
