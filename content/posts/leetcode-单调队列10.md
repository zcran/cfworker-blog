---
title: "leetcode-单调队列10"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 预算内的最多机器人数目

你有 n 个机器人，给你两个下标从 0 开始的整数数组 chargeTimes 和 runningCosts ，两者长度都为 n 。第 i 个机器人充电时间为 chargeTimes[i] 单位时间，花费 runningCosts[i] 单位时间运行。再给你一个整数 budget 。

运行 k 个机器人 总开销 是 max(chargeTimes) + k * sum(runningCosts) ，其中 max(chargeTimes) 是这 k 个机器人中最大充电时间，sum(runningCosts) 是这 k 个机器人的运行时间之和。

请你返回在 不超过 budget 的前提下，你 最多 可以运行的 连续 的机器人数目为多少。

```
use std::collections::VecDeque;

impl Solution {
    pub fn maximum_robots(charge_times: Vec<i32>, running_costs: Vec<i32>, budget: i64) -> i32 {
        let n = charge_times.len();
        if n == 0 {
            return 0;
        }

        // 将数据转为 i64 方便计算
        // 只转换一次
        // 代码中后续不需要 as i64，更简洁
        // 类型清晰，避免溢出风险
        // 注意使用 iter() 而不是 into_iter()，保留原数据的所有权
        let charge: Vec<i64> = charge_times.into_iter().map(|x| x as i64).collect();
        let cost: Vec<i64> = running_costs.into_iter().map(|x| x as i64).collect();

        let mut max_len = 0;
        let mut window_sum = 0i64;
        let mut deque: VecDeque<usize> = VecDeque::new(); // 存储下标，保证 charge[deque[i]] 递减
        let mut left = 0;

        for right in 0..n {
            // 1. 维护单调队列：加入新元素时，弹出队尾所有 ≤ 当前值的下标
            while let Some(&last) = deque.back() {
                if charge[last] <= charge[right] {
                    deque.pop_back();
                } else {
                    break;
                }
            }
            deque.push_back(right);
            window_sum += cost[right];

            // 2. 收缩窗口直到满足预算
            while left <= right {
                let max_charge = charge[*deque.front().unwrap()];
                let total = max_charge + (right - left + 1) as i64 * window_sum;
                if total <= budget {
                    break;
                }
                // 收缩左边界
                if let Some(&first) = deque.front() {
                    if first == left {
                        deque.pop_front();
                    }
                }
                window_sum -= cost[left];
                left += 1;
            }

            // 3. 更新答案
            max_len = max_len.max((right - left + 1) as i32);
        }

        max_len
    }
}
```
