---
title: "leetcode-拓扑排序19"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 参加会议的最多员工数

一个公司准备组织一场会议，邀请名单上有 n 位员工。公司准备了一张 圆形 的桌子，可以坐下 任意数目 的员工。

员工编号为 0 到 n - 1 。每位员工都有一位 喜欢 的员工，每位员工 当且仅当 他被安排在喜欢员工的旁边，他才会参加会议。每位员工喜欢的员工 不会 是他自己。

给你一个下标从 0 开始的整数数组 favorite ，其中 favorite[i] 表示第 i 位员工喜欢的员工。请你返回参加会议的 最多员工数目 。

```
use std::collections::VecDeque;

impl Solution {
    /// 返回最多可以邀请的雇员数量，使得每个人旁边都坐着他喜欢的人。
    /// 图由 `fav` 给出：`fav[i]` 表示 i 喜欢的人。
    /// 算法：内向基环树，答案 = max(最大环长度（环大小>2），所有二元环及其树链长度之和)
    pub fn maximum_invitations(fav: Vec<i32>) -> i32 {
        let n = fav.len();

        // ---------- 1. 拓扑排序，标记环上节点 ----------
        let mut indeg = vec![0; n];
        for &f in &fav {
            indeg[f as usize] += 1;
        }
        let mut q = VecDeque::new();
        for (i, &d) in indeg.iter().enumerate() {
            if d == 0 {
                q.push_back(i);
            }
        }
        let mut in_cycle = vec![true; n]; // true 表示节点在环上
        while let Some(u) = q.pop_front() {
            in_cycle[u] = false;
            let v = fav[u] as usize;
            indeg[v] -= 1;
            if indeg[v] == 0 {
                q.push_back(v);
            }
        }

        // ---------- 2. 计算每个非环节点到环上节点的步数（边数）及目标环上节点 ----------
        let mut depth = vec![0; n];          // depth[i] = 从 i 走到环上节点的边数
        let mut target = vec![n; n];         // target[i] = i 最终到达的环上节点（仅非环节点有效）
        for i in 0..n {
            if in_cycle[i] || depth[i] != 0 {
                continue;
            }
            // 用栈记录路径，避免递归
            let mut stack = Vec::new();
            let mut cur = i;
            while !in_cycle[cur] && depth[cur] == 0 {
                stack.push(cur);
                cur = fav[cur] as usize;
            }
            // cur 现在是环上节点 或 depth[cur] != 0 的节点
            let base_depth = if in_cycle[cur] { 0 } else { depth[cur] };
            let base_target = if in_cycle[cur] { cur } else { target[cur] };
            let mut d = base_depth + 1;
            while let Some(node) = stack.pop() {
                depth[node] = d;
                target[node] = base_target;
                d += 1;
            }
        }

        // ---------- 3. 计算每个环上节点能获得的最长链长度（步数） ----------
        let mut chain = vec![0; n];   // chain[v] = 以 v 为终点的最长链边数
        for u in 0..n {
            if in_cycle[u] {
                continue;
            }
            let v = target[u];          // u 最终到达的环上节点
            chain[v] = chain[v].max(depth[u]);
        }

        // ---------- 4. 处理所有环 ----------
        let mut visited = vec![false; n];
        let mut max_cycle = 0;          // 大小 > 2 的环的最大长度
        let mut sum_pairs = 0;          // 所有二元环（含树链）的总人数

        for i in 0..n {
            if !in_cycle[i] || visited[i] {
                continue;
            }
            // 收集当前环的所有节点
            let mut cycle = Vec::new();
            let mut cur = i;
            while !visited[cur] {
                visited[cur] = true;
                cycle.push(cur);
                cur = fav[cur] as usize;
            }
            let size = cycle.len();
            if size == 2 {
                let a = cycle[0];
                let b = cycle[1];
                let len_a = chain[a] + 1;   // 链长（包含环上节点自身）
                let len_b = chain[b] + 1;
                sum_pairs += len_a + len_b;
            } else {
                max_cycle = max_cycle.max(size as i32);
            }
        }

        max_cycle.max(sum_pairs)
    }
}
```
