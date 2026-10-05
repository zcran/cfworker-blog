---
title: "leetcode-树25"
date: 2026-09-24T09:47:34+08:00
tags: ["leetcode", "树"]
draft: false
---


## 树的加权和

给你一个长度为 n 的整数数组 parent，它表示一棵根节点编号为 0、节点编号范围为 0 到 n - 1 的有根树。

该树以节点 0 为 根节点，因此 parent[0] = -1。对于每个满足 1 <= i <= n - 1 的节点 i，parent[i] 表示节点 i 的父节点。

另给定一个长度为 n 的整数数组 nums，其中 nums[i] 表示节点 i 的值。

对于深度为 d 的节点 i，其 权重 定义为 nums[i] * (h - d + 1)，其中 h 表示树的高度。

返回树中所有节点的 权重之和 。

节点的 深度 定义为从根节点到该节点的路径上包含的节点数量，其中根节点的深度为 1。

树的 高度 定义为所有节点深度的最大值。


```
impl Solution {
    /// 题意化简：
    /// 节点 i 深度 d_i（根深度为 1），树高 h = max(d_i)。
    /// 权重 = nums[i] * (h - d_i + 1) = nums[i] * (h + 1) - nums[i] * d_i。
    /// 总和 = (h + 1) * Σ nums[i] - Σ nums[i] * d_i。
    ///
    /// 实现：BFS 逐层遍历，累计 Σ nums 与 Σ nums * depth。
    /// 注意：depth 从 0 开始计（根为第 0 层）更方便，此时 d_i = depth + 1，h = max_depth + 1。
    /// 则权重 = nums[i] * (max_depth - depth + 1)，
    /// 总和 = (max_depth + 1) * Σ nums - Σ nums * depth。
    pub fn weighted_sum(parent: Vec<i32>, nums: Vec<i32>) -> i64 {
        let n = parent.len();

        // 建图：父 -> 子
        let mut children = vec![Vec::new(); n];
        for i in 1..n {
            children[parent[i] as usize].push(i);
        }

        // BFS 逐层遍历，depth 从 0 开始
        let mut sum_nums = 0i64;      // Σ nums[i]
        let mut sum_weighted = 0i64;  // Σ nums[i] * depth
        let mut depth = 0i64;
        let mut queue = std::collections::VecDeque::new();
        queue.push_back(0usize);

        while !queue.is_empty() {
            let size = queue.len();
            for _ in 0..size {
                let u = queue.pop_front().unwrap();
                let val = nums[u] as i64;
                sum_nums += val;
                sum_weighted += val * depth;
                for &v in &children[u] {
                    queue.push_back(v);
                }
            }
            depth += 1;
        }

        // max_depth = depth - 1（最后一层处理完后 depth 多加了一次）
        let max_depth = depth - 1;
        (max_depth + 1) * sum_nums - sum_weighted
    }
}
```
