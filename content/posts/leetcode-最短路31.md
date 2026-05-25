---
title: "leetcode-最短路31"
date: 2026-04-22T20:45:21+08:00
tags: ["leetcode", "最短路"]
draft: false
---


## 电动车游城市

小明的电动车电量充满时可行驶距离为 cnt，每行驶 1 单位距离消耗 1 单位电量，且花费 1 单位时间。小明想选择电动车作为代步工具。地图上共有 N 个景点，景点编号为 0 ~ N-1。他将地图信息以 [城市 A 编号,城市 B 编号,两城市间距离] 格式整理在在二维数组 paths，表示城市 A、B 间存在双向通路。初始状态，电动车电量为 0。每个城市都设有充电桩，charge[i] 表示第 i 个城市每充 1 单位电量需要花费的单位时间。请返回小明最少需要花费多少单位时间从起点城市 start 抵达终点城市 end。



```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 电动汽车充电问题
    /// - `paths`: 无向边，每条边为 [u, v, 距离]
    /// - `cnt`: 电池最大容量（电量单位）
    /// - `start`: 起点编号
    /// - `end`: 终点编号
    /// - `charge`: 每个节点的充电时间（充 1 单位电所需时间）
    ///
    /// 返回从起点到终点的最短时间（移动 + 充电）
    pub fn electric_car_plan(
        paths: Vec<Vec<i32>>,
        cnt: i32,
        start: i32,
        end: i32,
        charge: Vec<i32>,
    ) -> i32 {
        let n = charge.len();
        let max_power = cnt as usize; // 最大电量
        let start = start as usize;
        let end = end as usize;

        // ---------- 1. 构建邻接表 ----------
        let mut graph = vec![vec![]; n];
        for e in paths {
            let u = e[0] as usize;
            let v = e[1] as usize;
            let len = e[2];
            graph[u].push((v, len));
            graph[v].push((u, len));
        }

        // ---------- 2. 状态编码 ----------
        // 状态总数 = 节点数 * (最大电量+1)
        let state_count = n * (max_power + 1);
        // 编码函数：将 (节点, 电量) 映射到唯一索引
        let encode = |u: usize, p: usize| u * (max_power + 1) + p;
        // 距离数组，初始化为一个大数（避免溢出）
        const INF: i32 = i32::MAX / 2;
        let mut dist = vec![INF; state_count];

        // ---------- 3. Dijkstra 算法 ----------
        let mut heap = BinaryHeap::new();
        let start_state = encode(start, 0);
        dist[start_state] = 0;
        heap.push(Reverse((0, start_state))); // (时间, 编码状态)

        while let Some(Reverse((time, state))) = heap.pop() {
            // 过时状态跳过
            if time > dist[state] {
                continue;
            }
            // 解码得到 (节点, 电量)
            let u = state / (max_power + 1);
            let power = state % (max_power + 1);
            // 到达终点，直接返回（Dijkstra 首次弹出即为最短）
            if u == end {
                return time;
            }

            // 操作 1：移动到相邻节点（消耗电量等于距离）
            for &(v, len) in &graph[u] {
                let len = len as usize; // 距离作为电量消耗
                if power >= len {
                    let new_power = power - len;
                    let new_state = encode(v, new_power);
                    let new_time = time + len as i32;
                    if new_time < dist[new_state] {
                        dist[new_state] = new_time;
                        heap.push(Reverse((new_time, new_state)));
                    }
                }
            }

            // 操作 2：在当前节点充电（增加 1 单位电量，最多到 max_power）
            if power < max_power {
                let new_power = power + 1;
                let new_state = encode(u, new_power);
                let new_time = time + charge[u];
                if new_time < dist[new_state] {
                    dist[new_state] = new_time;
                    heap.push(Reverse((new_time, new_state)));
                }
            }
        }

        // 理论上一定可达（题目保证有解？若不可达返回 0，原题未定义，此处返回 -1 表示不可达）
        -1
    }
}
```
