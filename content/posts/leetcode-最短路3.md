---
title: "leetcode-最短路3"
date: 2026-04-22T20:45:19+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## K 站中转内最便宜的航班


有 n 个城市通过一些航班连接。给你一个数组 flights ，其中 flights[i] = [fromi, toi, pricei] ，表示该航班都从城市 fromi 开始，以价格 pricei 抵达 toi。

现在给定所有的城市和航班，以及出发城市 src 和目的地 dst，你的任务是找到出一条最多经过 k 站中转的路线，使得从 src 到 dst 的 价格最便宜 ，并返回该价格。 如果不存在这样的路线，则输出 -1。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

const INF: i32 = i32::MAX;

impl Solution {
    /// 计算从 src 到 dst 的最便宜票价，最多允许 k 次中转（即最多 k+1 段航班）
    ///
    /// # 参数
    /// - `n`: 城市数量（编号 0..n-1）
    /// - `flights`: 航班列表，每个航班为 [from, to, price]
    /// - `src`: 起点城市
    /// - `dst`: 终点城市
    /// - `k`: 最多允许的中转次数
    ///
    /// # 返回值
    /// - 最便宜的票价，如果无法到达则返回 -1
    pub fn find_cheapest_price(n: i32, flights: Vec<Vec<i32>>, src: i32, dst: i32, k: i32) -> i32 {
        let n = n as usize;
        let src = src as usize;
        let dst = dst as usize;
        let k = k as usize; // 最多中转次数

        // ---------- 1. 构建邻接表 ----------
        // graph[from] = vec![(to, price), ...]
        let mut graph = vec![vec![]; n];
        for flight in flights {
            let from = flight[0] as usize;
            let to = flight[1] as usize;
            let price = flight[2];
            graph[from].push((to, price));
        }

        // ---------- 2. 距离表 ----------
        // dist[city][steps] = 从 src 出发，恰好经过 steps 段航班到达 city 的最小花费
        // steps 的取值范围为 0..=k+1（因为最多 k 次中转 = k+1 段航班）
        let max_steps = k + 1;
        let mut dist = vec![vec![INF; max_steps + 1]; n];
        // 起点: 0 段航班花费为 0，其余步数初始也设为 0（原代码做法，实际只用到 0 步也可以，但为了统一保留）
        for steps in 0..=max_steps {
            dist[src][steps] = 0;
        }

        // ---------- 3. 优先队列（最小堆）----------
        // 存储 (总花费, 当前城市, 已用航班段数)
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, src, 0)));

        while let Some(Reverse((price, city, steps))) = heap.pop() {
            // 到达目的地，直接返回当前花费（由于堆按花费递增，第一次遇到 dst 就是最优解）
            if city == dst {
                return price;
            }
            // 如果已经用完了允许的最大航班段数，不再继续扩展
            if steps >= max_steps {
                continue;
            }

            // 遍历当前城市的所有直飞航班
            for &(next_city, add_price) in &graph[city] {
                let next_price = price + add_price;
                let next_steps = steps + 1;
                // 只有当新花费比已知的「下一城市+相同步数」更小时才更新并入堆
                if next_price < dist[next_city][next_steps] {
                    dist[next_city][next_steps] = next_price;
                    heap.push(Reverse((next_price, next_city, next_steps)));
                }
            }
        }

        // 堆耗尽仍未到达目的地
        -1
    }
}
```
