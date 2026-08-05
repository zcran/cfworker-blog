---
title: "leetcode-枚举10"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 网络信号最好的坐标

给你一个数组 towers 和一个整数 radius 。

数组  towers  中包含一些网络信号塔，其中 towers[i] = [xi, yi, qi] 表示第 i 个网络信号塔的坐标是 (xi, yi) 且信号强度参数为 qi 。所有坐标都是在  X-Y 坐标系内的 整数 坐标。两个坐标之间的距离用 欧几里得距离 计算。

整数 radius 表示一个塔 能到达 的 最远距离 。如果一个坐标跟塔的距离在 radius 以内，那么该塔的信号可以到达该坐标。在这个范围以外信号会很微弱，所以 radius 以外的距离该塔是 不能到达的 。

如果第 i 个塔能到达 (x, y) ，那么该塔在此处的信号为 ⌊qi / (1 + d)⌋ ，其中 d 是塔跟此坐标的距离。一个坐标的 信号强度 是所有 能到达 该坐标的塔的信号强度之和。

请你返回数组 [cx, cy] ，表示 信号强度 最大的 整数 坐标点 (cx, cy) 。如果有多个坐标网络信号一样大，请你返回字典序最小的 非负 坐标。

注意：

坐标 (x1, y1) 字典序比另一个坐标 (x2, y2) 小，需满足以下条件之一：
  · 要么 x1 < x2 ，
  · 要么 x1 == x2 且 y1 < y2 。

⌊val⌋ 表示小于等于 val 的最大整数（向下取整函数）。


```
impl Solution {
    /// 找到信号强度最大的整数坐标点，若有多个则返回字典序最小的非负坐标。
    /// 所有塔和搜索范围限定在 [0, 50] 内（根据题目约束）。
    pub fn best_coordinate(towers: Vec<Vec<i32>>, radius: i32) -> Vec<i32> {
        let radius_sq = radius * radius;
        let mut best_signal = -1;
        let mut best_x = 0;
        let mut best_y = 0;

        // 坐标范围为 [0, 50]（题目隐含约束）
        for x in 0..=50 {
            for y in 0..=50 {
                let mut signal = 0;

                for tower in &towers {
                    let dx = tower[0] - x;
                    let dy = tower[1] - y;
                    let dist_sq = dx * dx + dy * dy;

                    // 距离在 radius 内才有信号
                    if dist_sq <= radius_sq {
                        let dist = (dist_sq as f64).sqrt();
                        signal += ((tower[2] as f64) / (1.0 + dist)) as i32;
                    }
                }

                // 更新最优解：信号更强，或信号相同但字典序更小
                if signal > best_signal || (signal == best_signal && (x < best_x || (x == best_x && y < best_y))) {
                    best_signal = signal;
                    best_x = x;
                    best_y = y;
                }
            }
        }

        vec![best_x, best_y]
    }
}
```
