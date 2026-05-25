---
title: "leetcode-记忆化36"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 快速公交

小扣打算去秋日市集，由于游客较多，小扣的移动速度受到了人流影响：

小扣从 x 号站点移动至 x + 1 号站点需要花费的时间为 inc；
小扣从 x 号站点移动至 x - 1 号站点需要花费的时间为 dec。

现有 m 辆公交车，编号为 0 到 m-1。小扣也可以通过搭乘编号为 i 的公交车，从 x 号站点移动至 jump[i]*x 号站点，耗时仅为 cost[i]。小扣可以搭乘任意编号的公交车且搭乘公交次数不限。

假定小扣起始站点记作 0，秋日市集站点记作 target，请返回小扣抵达秋日市集最少需要花费多少时间。由于数字较大，最终答案需要对 1000000007 (1e9 + 7) 取模。

注意：小扣可在移动过程中到达编号大于 target 的站点。

```
use std::collections::{BinaryHeap, HashMap};
use std::cmp::Reverse;

impl Solution {
    /// 计算从 0 到 target 的最短时间。
    /// 可执行操作：
    /// - 步行：位置 +1 花费 inc，位置 -1 花费 dec
    /// - 公交：从 x 瞬间跳到 x * jump[i]，花费 cost[i]
    /// 使用反向 Dijkstra（从 target 向 0 搜索），优先队列 + 记忆化最短距离。
    pub fn bus_rapid_transit(target: i32, inc: i32, dec: i32, jump: Vec<i32>, cost: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let target = target as i64;
        let inc = inc as i64;
        let dec = dec as i64;
        let jump: Vec<i64> = jump.into_iter().map(|x| x as i64).collect();
        let cost: Vec<i64> = cost.into_iter().map(|x| x as i64).collect();
        let n = jump.len();

        // 小顶堆：(累计时间, 当前位置)
        let mut heap = BinaryHeap::new();
        heap.push(Reverse((0, target)));
        // 记录每个位置的最短时间
        let mut dist = HashMap::new();
        dist.insert(target, 0);

        // 初始最优解：一直步行到 0
        let mut best = inc * target;

        while let Some(Reverse((time, pos))) = heap.pop() {
            // 如果当前时间不是该位置的最短时间，跳过
            if let Some(&d) = dist.get(&pos) {
                if time > d {
                    continue;
                }
            }
            // 从当前位置直接步行到 0 的时间
            best = best.min(time + pos * inc);
            // 若当前时间已经不小于全局最优，无需继续扩展
            if time >= best {
                continue;
            }

            for i in 0..n {
                let j = jump[i];
                let c = cost[i];
                let rem = pos % j;          // 到左边最近车站的距离
                let next = pos / j;          // 左边车站对应的整除位置

                if rem == 0 {
                    // 正好在公交站，直接反向乘车
                    let new_time = time + c;
                    if new_time < *dist.get(&next).unwrap_or(&i64::MAX) {
                        dist.insert(next, new_time);
                        heap.push(Reverse((new_time, next)));
                    }
                } else {
                    // 向左步行到车站（减小位置，对应正向步行前进）
                    let left_next = next;
                    let left_time = time + c + rem * inc;
                    if left_time < *dist.get(&left_next).unwrap_or(&i64::MAX) {
                        dist.insert(left_next, left_time);
                        heap.push(Reverse((left_time, left_next)));
                    }
                    // 向右步行到车站（增加位置，对应正向步行后退）
                    let right_next = next + 1;
                    let right_time = time + c + (j - rem) * dec;
                    if right_time < *dist.get(&right_next).unwrap_or(&i64::MAX) {
                        dist.insert(right_next, right_time);
                        heap.push(Reverse((right_time, right_next)));
                    }
                }
            }
        }

        (best % MOD) as i32
    }
}
```
