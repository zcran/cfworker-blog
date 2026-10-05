---
title: "leetcode-广度优先搜索17"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 公交路线

给你一个数组 routes ，表示一系列公交线路，其中每个 routes[i] 表示一条公交线路，第 i 辆公交车将会在上面循环行驶。

例如，路线 routes[0] = [1, 5, 7] 表示第 0 辆公交车会一直按序列 1 -> 5 -> 7 -> 1 -> 5 -> 7 -> 1 -> ... 这样的车站路线行驶。

现在从 source 车站出发（初始时不在公交车上），要前往 target 车站。 期间仅可乘坐公交车。

求出 最少乘坐的公交车数量 。如果不可能到达终点车站，返回 -1 。


```
use std::collections::{HashMap, HashSet, VecDeque};

impl Solution {
    pub fn num_buses_to_destination(routes: Vec<Vec<i32>>, source: i32, target: i32) -> i32 {
        if source == target {
            return 0;
        }

        // 站点 -> 经过该站的所有线路
        let mut stop2routes: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, r) in routes.iter().enumerate() {
            for &s in r.iter() {
                stop2routes.entry(s).or_default().push(i);
            }
        }

        // 起点/终点必须被至少一条线路覆盖
        let start_routes = match stop2routes.get(&source) {
            Some(v) => v,
            None => return -1,
        };
        if !stop2routes.contains_key(&target) {
            return -1;
        }

        let nr = routes.len();
        let mut vis_route = vec![false; nr];   // 线路是否已入队
        let mut vis_stop: HashSet<i32> = HashSet::new(); // 站点是否已展开换乘
        let mut q: VecDeque<(usize, i32)> = VecDeque::new();

        for &r in start_routes.iter() {
            vis_route[r] = true;
            q.push_back((r, 1));
        }

        while let Some((r, d)) = q.pop_front() {
            for &s in routes[r].iter() {
                if s == target {
                    return d;
                }
                // 每个站点只展开一次，避免重复扫描换乘列表
                if !vis_stop.insert(s) {
                    continue;
                }
                if let Some(rs) = stop2routes.get(&s) {
                    for &r2 in rs.iter() {
                        if !vis_route[r2] {
                            vis_route[r2] = true;
                            q.push_back((r2, d + 1));
                        }
                    }
                }
            }
        }

        -1
    }
}
```
