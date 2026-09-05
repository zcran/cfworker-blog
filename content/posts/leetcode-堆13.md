---
title: "leetcode-堆13"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最低加油次数

汽车从起点出发驶向目的地，该目的地位于出发位置东面 target 英里处。

沿途有加油站，用数组 stations 表示。其中 stations[i] = [positioni, fueli] 表示第 i 个加油站位于出发位置东面 positioni 英里处，并且有 fuelˇi 升汽油。

假设汽车油箱的容量是无限的，其中最初有 startFuel 升燃料。它每行驶 1 英里就会用掉 1 升汽油。当汽车到达加油站时，它可能停下来加油，将所有汽油从加油站转移到汽车中。

为了到达目的地，汽车所必要的最低加油次数是多少？如果无法到达目的地，则返回 -1 。

注意：如果汽车到达加油站时剩余燃料为 0，它仍然可以在那里加油。如果汽车到达目的地时剩余燃料为 0，仍然认为它已经到达目的地。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 贪心：每次经过加油站时把油量存入最大堆，油不够时从堆中取最大的油量加油
    pub fn min_refuel_stops(target: i32, mut start_fuel: i32, stations: Vec<Vec<i32>>) -> i32 {
        let mut heap = BinaryHeap::new(); // 存储经过但未加油的站点的油量
        let mut stops = 0;                // 加油次数
        let mut pos = 0;                  // 当前位置
        let mut i = 0;                    // 加油站索引

        while pos + start_fuel < target {
            // 模拟行驶到下一个加油站（或目的地），途中经过的站点都入堆
            while i < stations.len() && stations[i][0] <= pos + start_fuel {
                heap.push(stations[i][1]);
                i += 1;
            }

            // 没有可加油的站点，无法到达
            if heap.is_empty() {
                return -1;
            }

            // 在当前位置加满油（取经过站点中油量最大的），继续行驶
            start_fuel += heap.pop().unwrap();
            stops += 1;
        }

        stops
    }
}
```
