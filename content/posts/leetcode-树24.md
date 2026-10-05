---
title: "leetcode-树24"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 任务完成时间 I

给你一个整数 n，表示项目中的任务数量，编号从 0 到 n - 1。这些任务以任务 0 为根的 树 的形式连接。这由一个长度为 n - 1 的二维整数数组 edges 表示，其中 edges[i] = [ui, vi] 表示任务 ui 是任务 vi 的父节点。

同时给你一个长度为 n 的数组 baseTime，其中 baseTime[i] 表示完成任务 i 所需的时间。

每个任务的 完成时间 计算如下：

· 叶子任务：完成时间为 baseTime[i]。
· 非叶子任务：
    · 令 earliest 为其子节点中的 最小 完成时间，latest 为其子节点中的 最大 完成时间。
    · 令 ownDuration 为 (latest - earliest) + baseTime[i]。
    · 任务 i 的完成时间为 latest + ownDuration。

返回根任务 0 的完成时间。


```
impl Solution {
    /// 后序遍历 + 公式化简
    ///
    /// 题意：
    /// - 叶子任务完成时间 = baseTime[i]
    /// - 非叶子：
    ///     earliest = 子节点最小完成时间
    ///     latest   = 子节点最大完成时间
    ///     ownDuration = (latest - earliest) + baseTime[i]
    ///     完成时间 = latest + ownDuration
    ///              = latest + (latest - earliest) + baseTime[i]
    ///              = 2 * latest - earliest + baseTime[i]
    ///
    /// 直接按此公式后序 DFS 即可。
    pub fn finish_time(n: i32, edges: Vec<Vec<i32>>, base_time: Vec<i32>) -> i64 {
        let n = n as usize;

        // 建树：父 -> 子
        let mut children = vec![Vec::new(); n];
        for e in &edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            children[u].push(v);
        }

        // 迭代后序遍历，避免递归深度过大
        // 第一遍：生成后序顺序（父先入栈，逆序即为后序）
        let mut order = Vec::with_capacity(n);
        let mut stack = vec![0usize];
        while let Some(u) = stack.pop() {
            order.push(u);
            for &v in &children[u] {
                stack.push(v);
            }
        }

        // finish[u]：任务 u 的完成时间
        let mut finish = vec![0i64; n];

        // 逆序处理（自底向上）
        for &u in order.iter().rev() {
            if children[u].is_empty() {
                // 叶子任务
                finish[u] = base_time[u] as i64;
            } else {
                let mut earliest = i64::MAX;
                let mut latest = i64::MIN;
                for &v in &children[u] {
                    let t = finish[v];
                    if t < earliest {
                        earliest = t;
                    }
                    if t > latest {
                        latest = t;
                    }
                }
                // 完成时间 = 2 * latest - earliest + baseTime[u]
                finish[u] = 2 * latest - earliest + base_time[u] as i64;
            }
        }

        finish[0]
    }
}
```
