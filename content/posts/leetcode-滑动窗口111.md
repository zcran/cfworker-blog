---
title: "leetcode-滑动窗口111"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 使库存平衡的最少丢弃次数

给你两个整数 w 和 m，以及一个整数数组 arrivals，其中 arrivals[i] 表示第 i 天到达的物品类型（天数从 1 开始编号）。

物品的管理遵循以下规则：

· 每个到达的物品可以被 保留 或 丢弃 ，物品只能在到达当天被丢弃。
· 对于每一天 i，考虑天数范围为 [max(1, i - w + 1), i]（也就是直到第 i 天为止最近的 w 天）：
    · 对于 任何 这样的时间窗口，在被保留的到达物品中，每种类型最多只能出现 m 次。
    · 如果在第 i 天保留该到达物品会导致其类型在该窗口中出现次数 超过 m 次，那么该物品必须被丢弃。

返回为满足每个 w 天的窗口中每种类型最多出现 m 次，最少 需要丢弃的物品数量。


```
use std::collections::VecDeque;

impl Solution {
    pub fn min_arrivals_to_discard(arrivals: Vec<i32>, w: i32, m: i32) -> i32 {
        let w = w as usize;
        let m = m as usize;
        let mut freq = vec![0; 100_001];     // 类型范围 1..100000
        let mut kept = VecDeque::with_capacity(arrivals.len()); // 保留物品的索引
        let mut discard = 0;

        for (i, &item) in arrivals.iter().enumerate() {
            // 窗口左边界（包含）：max(0, i - w + 1)
            let left_bound = i.saturating_sub(w - 1);

            // 移除窗口左侧已离开的保留物品
            while let Some(&idx) = kept.front() {
                if idx < left_bound {
                    freq[arrivals[idx] as usize] -= 1;
                    kept.pop_front();
                } else {
                    break;
                }
            }

            // 尝试保留当前物品
            let typ = item as usize;
            if freq[typ] < m {
                freq[typ] += 1;
                kept.push_back(i);
            } else {
                discard += 1; // 该类型在窗口内已达上限，必须丢弃
            }
        }

        discard
    }
}
```
