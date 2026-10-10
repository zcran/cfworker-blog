---
title: "leetcode-数组14"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 公交站间的距离

环形公交路线上有 n 个站，按次序从 0 到 n - 1 进行编号。我们已知每一对相邻公交站之间的距离，distance[i] 表示编号为 i 的车站和编号为 (i + 1) % n 的车站之间的距离。

环线上的公交车都可以按顺时针和逆时针的方向行驶。

返回乘客从出发点 start 到目的地 destination 之间的最短距离。


```
impl Solution {
    /// 求环形公交路线上两站之间的最短距离。
    ///
    /// 先确保 `start <= destination`，则顺时针距离为
    /// `distance[start..destination]` 之和，逆时针距离为总距离减去它。
    /// 二者取较小值。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn distance_between_bus_stops(distance: Vec<i32>, start: i32, destination: i32) -> i32 {
        let (start, destination) = if start <= destination {
            (start as usize, destination as usize)
        } else {
            (destination as usize, start as usize)
        };

        let total: i32 = distance.iter().sum();
        let clockwise: i32 = distance[start..destination].iter().sum();

        clockwise.min(total - clockwise)
    }
}
```
