---
title: "leetcode-最短路15"
date: 2026-04-22T20:45:20+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 前往目标的最小代价


给你一个数组 start ，其中 start = [startX, startY] 表示你的初始位置位于二维空间上的 (startX, startY) 。另给你一个数组 target ，其中 target = [targetX, targetY] 表示你的目标位置 (targetX, targetY) 。

从位置 (x1, y1) 到空间中任一其他位置 (x2, y2) 的 代价 是 |x2 - x1| + |y2 - y1| 。

给你一个二维数组 specialRoads ，表示空间中存在的一些 特殊路径。其中 specialRoads[i] = [x1i, y1i, x2i, y2i, costi] 表示第 i 条特殊路径可以从 (x1i, y1i) 到 (x2i, y2i) ，但成本等于 costi 。你可以使用每条特殊路径任意次数。

返回从 (startX, startY) 到 (targetX, targetY) 所需的 最小 代价。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    /// 计算从起点到终点的最小代价，允许使用特殊道路（起点、终点、代价）。
    /// 普通移动为曼哈顿距离，特殊道路提供一条单向捷径。
    pub fn minimum_cost(start: Vec<i32>, target: Vec<i32>, special_roads: Vec<Vec<i32>>) -> i32 {
        // 起点等于终点时，无需任何移动，代价为 0
        if start == target {
            return 0;
        }

        // 将 (x, y) 坐标编码为 64 位整数：高 32 位存 x，低 32 位存 y
        let encode = |x: i32, y: i32| -> i64 {
            ((x as i64) << 32) | (y as i64 & 0xffff_ffff)
        };

        let start_code = encode(start[0], start[1]);
        let target_code = encode(target[0], target[1]);

        // 距离表：节点编码 -> 当前已知最短距离
        let mut dist: HashMap<i64, i32> = HashMap::new();
        dist.insert(start_code, 0);
        dist.insert(target_code, i32::MAX);

        let mut visited = HashSet::new();

        loop {
            // 在未访问节点中找出距离最小的节点（函数式风格：filter + min_by_key）
            let (&node, &d) = dist
              .iter()
              .filter(|(k, _)| !visited.contains(*k))
              .min_by_key(|(_, d)| *d)
              .unwrap();

            // 若选中的节点就是终点，则其距离已是最优解
            if node == target_code {
                return d;
            }

            visited.insert(node);

            // 解码节点的坐标
            let vx = (node >> 32) as i32;
            let vy = (node & 0xffff_ffff) as i32;

            // 更新从当前节点直接走到终点的距离（普通曼哈顿移动）
            let direct_dist = d + (target[0] - vx).abs() + (target[1] - vy).abs();
            dist.entry(target_code)
                .and_modify(|cur| *cur = (*cur).min(direct_dist))
                .or_insert(direct_dist);

            // 尝试使用每一条特殊道路，更新道路终点的距离
            for road in &special_roads {
                let x1 = road[0];
                let y1 = road[1];
                let x2 = road[2];
                let y2 = road[3];
                let road_cost = road[4];

                let to_road_start = d + (x1 - vx).abs() + (y1 - vy).abs();
                let total = to_road_start + road_cost;

                let road_end = encode(x2, y2);
                dist.entry(road_end)
                    .and_modify(|cur| *cur = (*cur).min(total))
                    .or_insert(total);
            }
        }
    }
}
```
