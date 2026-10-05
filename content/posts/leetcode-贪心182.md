---
title: "leetcode-贪心182"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 交通灯的最大等待时间

给你一个整数 period 和一个整数数组 lights，其中 lights[i] 表示第 i^th 个交通信号灯绿灯阶段的持续时间（单位为秒）。

在时间 0，所有交通信号灯均从绿灯阶段开始运行。它们的周期是同步的：所有交通信号灯会同时开始新的周期，并且每个周期的持续时间恰好为 period 秒。因此，第 i^th 个交通信号灯的红灯阶段持续 period - lights[i] 秒。

另给你一个整数数组 arrivalTime，其中 arrivalTime[j] 表示第 j^th 辆汽车的到达时间（单位为秒）。

每辆汽车必须被分配到恰好一个交通信号灯。多辆汽车可以被分配到同一个交通信号灯。绿灯亮起时，任意数量的汽车都可以同时通过同一个交通信号灯。汽车之间不会互相阻挡或造成延误。

对于被分配到第 i^th 个交通信号灯的汽车 j，令 r = arrivalTime[j] % period。如果 r < lights[i]，则其等待时间为 0。否则，其等待时间为 period - r。

一种分配方案的惩罚值是所有汽车等待时间中的最大值。

返回一个整数，表示可能得到的最小惩罚值。




```
impl Solution {
    pub fn min_penalty(period: i32, lights: Vec<i32>, arrival_time: Vec<i32>) -> i32 {
        // 关键观察：一辆车若在某个绿灯时长 >= r 的灯下，等待时间为 0。
        // 全局最长的绿灯时长 mx = max(lights) 是最"万能"的选择：
        //   - 若 r < mx，把它分配给这盏灯，等待 0，不影响答案；
        //   - 若 r >= mx，无论分给哪盏灯都无法绿灯通过，等待时间为 period - r。
        // 因此答案 = 所有 r >= mx 的到达时间中，period - r 的最大值。
        // （其余车辆统一交给那盏最长的绿灯即可，等待都是 0。）
        let mx = *lights.iter().max().unwrap();
        let mut penalty = 0;

        for t in arrival_time {
            let r = t % period;
            if r >= mx {
                penalty = penalty.max(period - r);
            }
        }
        penalty
    }
}
```
