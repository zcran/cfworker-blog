---
title: "leetcode-拓扑排序20"
date: 2026-04-26T21:00:35+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 给定条件下构造矩阵

给你一个 正 整数 k ，同时给你：

一个大小为 n 的二维整数数组 rowConditions ，其中 rowConditions[i] = [abovei, belowi] 和
一个大小为 m 的二维整数数组 colConditions ，其中 colConditions[i] = [lefti, righti] 。
两个数组里的整数都是 1 到 k 之间的数字。

你需要构造一个 k x k 的矩阵，1 到 k 每个数字需要 恰好出现一次 。剩余的数字都是 0 。

矩阵还需要满足以下条件：

对于所有 0 到 n - 1 之间的下标 i ，数字 abovei 所在的 行 必须在数字 belowi 所在行的上面。
对于所有 0 到 m - 1 之间的下标 i ，数字 lefti 所在的 列 必须在数字 righti 所在列的左边。
返回满足上述要求的 任意 矩阵。如果不存在答案，返回一个空的矩阵。


```
use std::collections::VecDeque;

impl Solution {
    /// 对 1..=k 的节点进行拓扑排序，结果存入 `order`。
    /// 如果图中存在环，则 `order` 会被清空。
    ///
    /// # 参数
    /// - `k`: 节点总数（编号从 1 到 k）
    /// - `edges`: 有向边列表，每条边为 `[from, to]`
    /// - `order`: 用于存放拓扑序的可变引用
    fn topo_sort(k: usize, edges: Vec<Vec<i32>>, order: &mut Vec<i32>) {
        // 邻接表：graph[u] 存储所有以 u 为起点的终点节点（使用 0-based 索引存储 usize）
        let mut graph = vec![Vec::new(); k + 1];
        // 入度数组，下标对应节点编号
        let mut indegree = vec![0; k + 1];

        // 构建图并统计入度
        for edge in edges {
            let from = edge[0] as usize;
            let to = edge[1] as usize;
            graph[from].push(to);
            indegree[to] += 1;
        }

        // 收集初始入度为 0 的节点，使用 VecDeque 以便高效弹出
        let mut zero_queue: VecDeque<usize> = (1..=k)
            .filter(|&node| indegree[node] == 0)
            .collect();

        // 拓扑排序主循环
        while let Some(current) = zero_queue.pop_front() {
            order.push(current as i32);          // 记录当前节点到拓扑序
            for &next in &graph[current] {       // 遍历当前节点的所有后继
                indegree[next] -= 1;
                if indegree[next] == 0 {
                    zero_queue.push_back(next);  // 新产生的入度为 0 节点入队
                }
            }
        }

        // 若排序节点数量不等于总节点数，说明存在环，清空结果
        if order.len() != k {
            order.clear();
        }
    }

    /// 根据行条件和列条件构建一个 k×k 的矩阵。
    /// 矩阵中的数字 1..=k 各出现一次，且每行的相对顺序满足 `row_conditions`，
    /// 每列的相对顺序满足 `col_conditions`。若条件矛盾（存在环），返回空矩阵。
    ///
    /// # 参数
    /// - `k`: 矩阵的边长及最大数字
    /// - `row_conditions`: 行约束，每条 `[a, b]` 表示 a 必须出现在 b 的左侧
    /// - `col_conditions`: 列约束，每条 `[a, b]` 表示 a 必须出现在 b 的上方
    ///
    /// # 返回
    /// 一个 `k×k` 的二维 `Vec`，若无法构造则返回空 `Vec`。
    pub fn build_matrix(
        k: i32,
        row_conditions: Vec<Vec<i32>>,
        col_conditions: Vec<Vec<i32>>,
    ) -> Vec<Vec<i32>> {
        let k = k as usize;          // 转为 usize 便于索引
        let mut row_order = Vec::new();
        let mut col_order = Vec::new();

        // 分别对行和列进行拓扑排序
        Self::topo_sort(k, row_conditions, &mut row_order);
        Self::topo_sort(k, col_conditions, &mut col_order);

        // 任何一个拓扑序为空（存在环）则无法构建矩阵
        if row_order.is_empty() || col_order.is_empty() {
            return Vec::new();
        }

        // position[i] = [行索引, 列索引]   i 取值范围 1..=k
        let mut position = vec![[0, 0]; k + 1];

        // 根据行序确定每个数字的行坐标（行序中的位置即该数字所在行）
        for (row_idx, &val) in row_order.iter().enumerate() {
            position[val as usize][0] = row_idx;
        }
        // 根据列序确定每个数字的列坐标
        for (col_idx, &val) in col_order.iter().enumerate() {
            position[val as usize][1] = col_idx;
        }

        // 构造结果矩阵，初始全 0
        let mut matrix = vec![vec![0; k]; k];
        for num in 1..=k {
            let [row, col] = position[num];
            matrix[row][col] = num as i32;
        }

        matrix
    }
}
```
