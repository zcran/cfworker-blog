---
title: "leetcode-枚举27"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 节点序列的最大得分

给你一个 n 个节点的 无向图 ，节点编号为 0 到 n - 1 。

给你一个下标从 0 开始的整数数组 scores ，其中 scores[i] 是第 i 个节点的分数。同时给你一个二维整数数组 edges ，其中 edges[i] = [ai, bi] ，表示节点 ai 和 bi 之间有一条 无向 边。

一个合法的节点序列如果满足以下条件，我们称它是 合法的 ：

序列中每 相邻 节点之间有边相连。
序列中没有节点出现超过一次。

节点序列的分数定义为序列中节点分数之 和 。

请你返回一个长度为 4 的合法节点序列的最大分数。如果不存在这样的序列，请你返回 -1 。


```
use std::cmp::Ordering;

impl Solution {
    pub fn maximum_score(scores: Vec<i32>, edges: Vec<Vec<i32>>) -> i32 {
        let n = scores.len();

        // 邻接表：存储每个节点的邻居及对应的分数（取负以便按分数降序排序）
        let mut adj: Vec<Vec<(i32, usize)>> = vec![vec![]; n];
        for e in &edges {
            let u = e[0] as usize;
            let v = e[1] as usize;
            // 存储负分数，使得排序时分数高的在前面（因为负值越小表示原值越大）
            adj[u].push((-scores[v], v));
            adj[v].push((-scores[u], u));
        }

        // 每个节点只保留分数最高的 3 个邻居（因为长度为4的序列只需要中间节点的最好邻居）
        for neighbors in adj.iter_mut() {
            if neighbors.len() > 3 {
                // 使用选择算法找出前3大，效率 O(n) vs O(n log n)
                neighbors.select_nth_unstable(2);
                neighbors.truncate(3);
            }
        }

        let mut ans = -1;
        // 枚举每条边作为序列的中间边
        for e in &edges {
            let x = e[0] as usize;
            let y = e[1] as usize;
            let score_x = scores[x];
            let score_y = scores[y];

            // 从 x 的邻居中选择一个作为序列的第一个节点
            for &(neg_score_a, a) in &adj[x] {
                // 从 y 的邻居中选择一个作为序列的第四个节点
                for &(neg_score_b, b) in &adj[y] {
                    // 四个节点必须互不相同
                    if a != y && b != x && a != b {
                        // 计算总分数：score_a + score_x + score_y + score_b
                        let total = -neg_score_a + score_x + score_y - neg_score_b;
                        ans = ans.max(total);
                    }
                }
            }
        }

        ans
    }
}
```
