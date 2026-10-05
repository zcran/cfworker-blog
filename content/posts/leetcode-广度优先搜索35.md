---
title: "leetcode-广度优先搜索35"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 到家的最少跳跃次数

有一只跳蚤的家在数轴上的位置 x 处。请你帮助它从位置 0 出发，到达它的家。

跳蚤跳跃的规则如下：

它可以 往前 跳恰好 a 个位置（即往右跳）。
它可以 往后 跳恰好 b 个位置（即往左跳）。
它不能 连续 往后跳 2 次。
它不能跳到任何 forbidden 数组中的位置。

跳蚤可以往前跳 超过 它的家的位置，但是它 不能跳到负整数 的位置。

给你一个整数数组 forbidden ，其中 forbidden[i] 是跳蚤不能跳到的位置，同时给你整数 a， b 和 x ，请你返回跳蚤到家的最少跳跃次数。如果没有恰好到达 x 的可行方案，请你返回 -1 。


```
use std::collections::{HashSet, VecDeque};

impl Solution {
    pub fn minimum_jumps(forbidden: Vec<i32>, a: i32, b: i32, x: i32) -> i32 {
        // 计算搜索上界：
        //  - a >= b 时，跳蚤无需越过 x + b（因为回跳距离 ≤ 前跳距离）
        //  - a < b 时，回跳可能让跳蚤先越过 x 再回来，上界取 x 与 a+b+max(forbidden) 的较大者
        let max_forbidden = forbidden.iter().copied().max().unwrap_or(0);
        let limit = if a >= b {
            x + b
        } else {
            x.max(a + b + max_forbidden)
        };

        let forbidden: HashSet<i32> = forbidden.into_iter().collect();

        // 状态：(当前位置, 上一步是否是后退)
        // 同一位置 + 同一“上一步方向”只需访问一次
        let mut visited: HashSet<(i32, bool)> = HashSet::new();
        let mut queue = VecDeque::new();

        queue.push_back((0, false));
        visited.insert((0, false));

        let mut steps = 0;
        while !queue.is_empty() {
            // 逐层 BFS，每层代表一步
            for _ in 0..queue.len() {
                let (pos, came_from_back) = queue.pop_front().unwrap();
                if pos == x {
                    return steps;
                }

                // 1. 向前跳 a
                let next = pos + a;
                if next <= limit && !forbidden.contains(&next) && visited.insert((next, false)) {
                    queue.push_back((next, false));
                }

                // 2. 向后跳 b（不能连续后退两次）
                let next = pos - b;
                if !came_from_back
                    && next > 0
                    && !forbidden.contains(&next)
                    && visited.insert((next, true))
                {
                    queue.push_back((next, true));
                }
            }
            steps += 1;
        }

        -1
    }
}
```
