---
title: "leetcode-动态规划177"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最小化最大可能等待时间

给你一个整数数组 demand，其中 demand[i] 是第 i 辆车需要的燃料量。

同时给你一个长度为 2 的整数数组 fuel。有 恰好 两个加油机，编号为 0 和 1，其中 fuel[j] 是加油机 j 中可用的初始燃料量。

允许车辆按 递增 的下标顺序开始加油。第 0 辆车在时间 0 被允许加油，对于每个 i > 0，第 i 辆车 恰好 在第 i - 1 辆车开始加油时被允许加油。

加油过程遵循以下规则：

每个加油机一次 最多 只能服务一辆车。
只有当加油机空闲且剩余燃料 至少 为 demand[i] 时，车辆才能在该加油机开始加油。
汽车等待所选加油机空闲后 立即 开始加油。它不能切换加油机或在所选加油机空闲后故意等待。
给一辆车加油需要 demand[i] 秒，并将该加油机的剩余燃料减少 demand[i]。
一旦开始，加油过程不能被中断。
当两个加油机都空闲时，如果没有任何一个加油机的剩余燃料 至少 为 demand[i]，则过程终止，且无法再服务更多车辆。


车辆的 等待时间 是从它被允许开始加油到实际开始加油之间的时间。

在 最大化 被服务车辆数量的所有分配方案中，返回所有被服务车辆中 最大 等待时间的 最小 可能值。如果没有车辆可以被服务，返回 -1。


```
use std::collections::HashMap;

impl Solution {
    pub fn min_max_waiting_time(demand: Vec<i32>, fuel: Vec<i32>) -> i32 {
        // 状态编码：(i, wait1, fuel0, fuel1) -> (max_num, best_wait_time)
        // 每位用 6 位表示（fuel <= 63 时足够），i 放高位
        let mut memo: HashMap<u32, (i32, i32)> = HashMap::new();

        fn dfs(
            i: usize,
            wait1: i32,
            fuel0: i32,
            fuel1: i32,
            demand: &[i32],
            memo: &mut HashMap<u32, (i32, i32)>,
        ) -> (i32, i32) {
            if i == demand.len() {
                return (0, 0);
            }

            let key = ((i as u32) << 18)
                | ((wait1 as u32) << 12)
                | ((fuel0 as u32) << 6)
                | (fuel1 as u32);
            if let Some(&v) = memo.get(&key) {
                return v;
            }

            let d = demand[i];
            // wait0：若 i>0，表示当前车相对上一辆车的允许加油时刻差
            let wait0 = if i > 0 { demand[i - 1] } else { 0 };

            let mut max_num = 0;
            let mut best_wait = 0;

            // 选择加油机 0：需等它空闲，等待时间为 max(wait1 - wait0, 0)
            if d <= fuel0 {
                let (num, time) =
                    dfs(i + 1, (wait1 - wait0).max(0), fuel0 - d, fuel1, demand, memo);
                max_num = num + 1;
                best_wait = time.max(wait0);
            }

            // 选择加油机 1：交换两个加油机的角色
            if d <= fuel1 {
                let (num, time) =
                    dfs(i + 1, (wait0 - wait1).max(0), fuel1 - d, fuel0, demand, memo);
                let num = num + 1;
                let time = time.max(wait1);
                if num > max_num || (num == max_num && time < best_wait) {
                    max_num = num;
                    best_wait = time;
                }
            }

            memo.insert(key, (max_num, best_wait));
            (max_num, best_wait)
        }

        let (max_num, best_wait) = dfs(0, 0, fuel[0], fuel[1], &demand, &mut memo);
        if max_num == 0 { -1 } else { best_wait }
    }
}
```
