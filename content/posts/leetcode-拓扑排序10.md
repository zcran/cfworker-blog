---
title: "leetcode-拓扑排序10"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 矩阵转换后的排名


给你一个 m x n 的矩阵 matrix ，请你返回一个新的矩阵 answer ，其中 answer[row][col] 是 matrix[row][col] 的排名。

每个元素的 排名 是一个整数，表示这个元素相对于其他元素的大小关系，它按照如下规则计算：

排名是从 1 开始的一个整数。
如果两个元素 p 和 q 在 同一行 或者 同一列 ，那么：
如果 p < q ，那么 rank(p) < rank(q)
如果 p == q ，那么 rank(p) == rank(q)
如果 p > q ，那么 rank(p) > rank(q)
排名 需要越 小 越好。
题目保证按照上面规则 answer 数组是唯一的。

```
use std::collections::HashMap;

impl Solution {
    /// 矩阵的秩变换（LeetCode 1632）
    /// 将矩阵中每个元素替换为它在原矩阵中的“秩”，满足：
    /// - 同行或同列中值相同的元素秩相同
    /// - 值更大的元素秩更大
    /// - 所有秩尽可能小
    pub fn matrix_rank_transform(matrix: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let m = matrix.len();                 // 矩阵的行数
        let n = matrix[0].len();              // 矩阵的列数
        let mut result = vec![vec![0; n]; m]; // 初始化结果矩阵，全部填0

        // ---------- 阶段1：将所有元素按值排序 ----------
        // 借用 matrix 的引用（引用是 Copy 的，可以在 move 闭包中安全使用）
        let mat_ref = &matrix;
        // 生成所有 (值, 行, 列) 的元组，并按值升序排序
        let mut elements: Vec<_> = (0..m)
            .flat_map(|i| (0..n).map(move |j| (mat_ref[i][j], i, j)))
            .collect();
        elements.sort_unstable();    // 不稳定排序，性能更好

        // row_max[r] 表示第 r 行当前已分配的最大秩
        // col_max[c] 表示第 c 列当前已分配的最大秩
        let (mut row_max, mut col_max) = (vec![0; m], vec![0; n]);
        let mut idx = 0;             // 当前处理到 elements 中的位置

        // ---------- 阶段2：按值分组处理 ----------
        while idx < elements.len() {
            let val = elements[idx].0;   // 当前组的值

            // 收集所有值等于 val 的位置 (行, 列) 到 group 中
            let group: Vec<_> = elements[idx..]
                .iter()
                .take_while(|&&(v, _, _)| v == val)
                .map(|&(_, r, c)| (r, c))
                .collect();
            idx += group.len();          // 跳过已处理的组

            // ---------- 并查集初始化 ----------
            // parent 数组用于并查集，每个元素初始指向自己
            let mut parent: Vec<_> = (0..group.len()).collect();

            // 查找根节点（带路径压缩）
            fn find(parent: &mut Vec<usize>, x: usize) -> usize {
                if parent[x] != x {
                    parent[x] = find(parent, parent[x]);
                }
                parent[x]
            }

            // 合并两个集合
            fn union(parent: &mut Vec<usize>, a: usize, b: usize) {
                let ra = find(parent, a);
                let rb = find(parent, b);
                if ra != rb {
                    parent[rb] = ra;
                }
            }

            // ---------- 按行合并：相同值的元素如果在同一行，必须属于同一个并查集 ----------
            let mut row_map = HashMap::new(); // 记录每行第一次出现的元素索引
            for (i, &(r, _)) in group.iter().enumerate() {
                if let Some(&prev) = row_map.get(&r) {
                    union(&mut parent, prev, i); // 同一行，合并
                } else {
                    row_map.insert(r, i);        // 首次出现，记录索引
                }
            }

            // ---------- 按列合并：相同值的元素如果在同一列，也必须属于同一个并查集 ----------
            let mut col_map = HashMap::new(); // 记录每列第一次出现的元素索引
            for (i, &(_, c)) in group.iter().enumerate() {
                if let Some(&prev) = col_map.get(&c) {
                    union(&mut parent, prev, i); // 同一列，合并
                } else {
                    col_map.insert(c, i);        // 首次出现，记录索引
                }
            }

            // ---------- 计算每个连通分量的秩 ----------
            // comp_rank[root] = 该根对应集合中所有元素应该被赋予的秩
            let mut comp_rank: HashMap<usize, usize> = HashMap::new();
            for i in 0..group.len() {
                let root = find(&mut parent, i);        // 找到当前元素所属集合的根
                let (r, c) = group[i];                  // 当前元素的行、列
                // 候选秩 = max(该行已有的最大秩, 该列已有的最大秩) + 1
                let candidate = (row_max[r] + 1).max(col_max[c] + 1);
                // 对于同一个集合，取所有元素候选秩的最大值作为该集合的最终秩
                comp_rank
                    .entry(root)
                    .and_modify(|r| *r = (*r).max(candidate))
                    .or_insert(candidate);
            }

            // ---------- 更新结果矩阵及 row_max / col_max ----------
            for i in 0..group.len() {
                let root = find(&mut parent, i);        // 根节点
                let rank = comp_rank[&root];            // 该集合的秩
                let (r, c) = group[i];                  // 当前元素的位置
                result[r][c] = rank as i32;             // 填入结果矩阵
                // 更新该行和该列的最大秩（供后续更大的值使用）
                row_max[r] = row_max[r].max(rank);
                col_max[c] = col_max[c].max(rank);
            }
        }

        result  // 返回最终的秩矩阵
    }
}
```
