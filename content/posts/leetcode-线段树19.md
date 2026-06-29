---
title: "leetcode-线段树19"
date: 2026-06-19T09:37:25+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 最小跳跃次数

为了给刷题的同学一些奖励，力扣团队引入了一个弹簧游戏机。游戏机由 N 个特殊弹簧排成一排，编号为 0 到 N-1。初始有一个小球在编号 0 的弹簧处。若小球在编号为 i 的弹簧处，通过按动弹簧，可以选择把小球向右弹射 jump[i] 的距离，或者向左弹射到任意左侧弹簧的位置。也就是说，在编号为 i 弹簧处按动弹簧，小球可以弹向 0 到 i-1 中任意弹簧或者 i+jump[i] 的弹簧（若 i+jump[i]>=N ，则表示小球弹出了机器）。小球位于编号 0 处的弹簧时不能再向左弹。

为了获得奖励，你需要将小球弹出机器。请求出最少需要按动多少次弹簧，可以将小球从编号 0 弹簧弹出整个机器，即向右越过编号 N-1 的弹簧。


```
use std::collections::VecDeque;

impl Solution {
    pub fn min_jump(jump: Vec<i32>) -> i32 {
        let n = jump.len();

        // ---------- 1. 建立反向图 ----------
        // rev[i] 存储所有能通过向右跳到达 i 的节点
        let mut rev = vec![Vec::new(); n];
        // can_exit[i] 表示从 i 可以直接弹出
        let mut can_exit = vec![false; n];

        for i in 0..n {
            let nxt = i + jump[i] as usize;
            if nxt >= n {
                can_exit[i] = true;
            } else {
                rev[nxt].push(i);
            }
        }

        // ---------- 2. 反向 BFS ----------
        let mut dist = vec![-1; n];
        let mut queue = VecDeque::new();

        // 所有能直接弹出的节点作为起点，距离为1
        for i in 0..n {
            if can_exit[i] {
                dist[i] = 1;
                queue.push_back(i);
            }
        }

        if dist[0] != -1 {
            return 1;
        }

        // 优化：记录已访问节点的最左位置
        // 所有 >= left_bound 的节点都已访问过，不需要重复遍历
        let mut left_bound = n;

        while let Some(cur) = queue.pop_front() {
            let steps = dist[cur];

            // 2.1 通过向右跳到达 cur 的节点
            for &prev in &rev[cur] {
                if dist[prev] == -1 {
                    dist[prev] = steps + 1;
                    if prev == 0 {
                        return steps + 1;
                    }
                    queue.push_back(prev);
                }
            }

            // 2.2 通过向左跳到达 cur 的节点
            // 所有 > cur 的节点都可以向左跳到 cur
            // 只遍历未访问的节点 [cur+1, left_bound)
            for prev in (cur + 1)..left_bound {
                if dist[prev] == -1 {
                    dist[prev] = steps + 1;
                    if prev == 0 {
                        return steps + 1;
                    }
                    queue.push_back(prev);
                }
            }
            // 更新边界：cur 左侧的节点都已被访问
            left_bound = left_bound.min(cur);
        }

        -1
    }
}
```
