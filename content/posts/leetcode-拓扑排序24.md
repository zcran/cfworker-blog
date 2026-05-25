---
title: "leetcode-拓扑排序24"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 有向无环图中合法拓扑排序的最大利润

给你一个由 n 个节点组成的有向无环图（DAG），节点编号从 0 到 n - 1，通过二维数组 edges 表示，其中 edges[i] = [ui, vi] 表示一条从节点 ui 指向节点 vi 的有向边。每个节点都有一个对应的 得分 ，由数组 score 给出，其中 score[i] 表示节点 i 的得分。

你需要以 有效的拓扑排序 顺序处理这些节点。每个节点在处理顺序中被分配一个编号从 1 开始的位置。

将每个节点的得分乘以其在拓扑排序中的位置，然后求和，得到的值称为 利润。

请返回在所有合法拓扑排序中可获得的 最大利润 。

拓扑排序 是一个对 DAG 中所有节点的线性排序，使得每条有向边 u → v 中，节点 u 都出现在 v 之前。

```
impl Solution {
    /// 计算学习所有课程的最大总得分。
    /// 每门课 `score[i]`，第 `t` 个学的课程得分乘以 `t`。
    /// `edges` 中的 `[a, b]` 表示必须先学 `a` 才能学 `b`。
    /// 若无先修关系，直接按分数升序排列（小分数先学）可得最优解。
    pub fn max_profit(n: i32, edges: Vec<Vec<i32>>, score: Vec<i32>) -> i32 {
        let n = n as usize;
        // ----- 无先修关系：贪心（升序） -----
        if edges.is_empty() {
            let mut sorted = score.clone();
            sorted.sort();                     // 升序
            return sorted.iter()
                .enumerate()
                .map(|(i, &val)| val * (i as i32 + 1))
                .sum();
        }

        // ----- 构建每个课程的先修掩码 -----
        let mut pre = vec![0usize; n];
        for e in &edges {
            let a = e[0] as usize;
            let b = e[1] as usize;
            pre[b] |= 1 << a;
        }

        let total = 1 << n;
        let mut dp = vec![-1; total];
        dp[0] = 0;

        // ----- 状态压缩 DP（自底向上）-----
        for s in 0..total {
            if dp[s] < 0 { continue; }          // 不可达状态
            let learned = s.count_ones() as i32; // 已学数量
            for j in 0..n {
                if (s >> j) & 1 == 0 && (s | pre[j]) == s {
                    let ns = s | (1 << j);
                    let val = dp[s] + score[j] * (learned + 1);
                    if val > dp[ns] {
                        dp[ns] = val;
                    }
                }
            }
        }

        dp[total - 1]   // 学完所有课程的最大得分
    }
}
```
