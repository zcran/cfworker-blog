---
title: "leetcode-数组71"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最近的可用无人机

给你一个二维整数数组 drones，其中 drones[i] = [xi, yi, rangei] 表示第 ith 架无人机的横坐标、纵坐标和飞行范围。

另给你一个整数数组 target = [tx, ty]，表示目标的坐标。

如果无人机 drones[i] 的坐标与目标坐标之间的曼哈顿距离小于或等于其 rangei，则该无人机能够到达目标。

返回能够到达目标且与目标之间曼哈顿距离最小的无人机的下标。如果存在多个符合条件的无人机，则返回其中最小的下标。如果没有无人机能够到达目标，则返回 -1。

两个坐标 (xi, yi) 和 (xj, yj) 之间的曼哈顿距离为 |xi - xj| + |yi - yj|。


```
impl Solution {
    pub fn nearest_drone(drones: Vec<Vec<i32>>, target: Vec<i32>) -> i32 {
        let (tx, ty) = (target[0], target[1]);

        drones
            .iter()
            .enumerate()
            // 只保留能到达目标的无人机，按 (曼哈顿距离, 下标) 取最小
            .filter_map(|(i, d)| {
                let dis = (tx - d[0]).abs() + (ty - d[1]).abs();
                (dis <= d[2]).then_some((dis, i))
            })
            .min()
            .map_or(-1, |(_, i)| i as i32)
    }
}
```
