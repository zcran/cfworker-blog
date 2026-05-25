---
title: "leetcode-位掩码13"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 统计子树中城市之间最大距离

给你 n 个城市，编号为从 1 到 n 。同时给你一个大小为 n-1 的数组 edges ，其中 edges[i] = [ui, vi] 表示城市 ui 和 vi 之间有一条双向边。题目保证任意城市之间只有唯一的一条路径。换句话说，所有城市形成了一棵 树 。

一棵 子树 是城市的一个子集，且子集中任意城市之间可以通过子集中的其他城市和边到达。两个子树被认为不一样的条件是至少有一个城市在其中一棵子树中存在，但在另一棵子树中不存在。

对于 d 从 1 到 n-1 ，请你找到城市间 最大距离 恰好为 d 的所有子树数目。

请你返回一个大小为 n-1 的数组，其中第 d 个元素（下标从 1 开始）是城市间 最大距离 恰好等于 d 的子树数目。

请注意，两个城市间距离定义为它们之间需要经过的边的数目。

```
impl Solution {
    pub fn count_subgraphs_for_each_diameter(n: i32, edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;
        let graph = edges.iter().fold(vec![vec![]; n], |mut g, e| {
            let u = (e[0] - 1) as usize;
            let v = (e[1] - 1) as usize;
            g[u].push(v);
            g[v].push(u);
            g
        });

        let full_mask = 1 << n;
        // 使用 1usize 明确类型，fold 代替 for 循环
        (1usize..full_mask)
            .filter(|&mask| mask.count_ones() > 1 && is_connected_rec(mask, &graph))
            .fold(vec![0; n - 1], |mut ans, mask| {
                let diam = tree_diameter_rec(mask, &graph);
                ans[diam - 1] += 1;
                ans
            })
    }
}

/// 递归 DFS 检查连通性
fn is_connected_rec(mask: usize, graph: &[Vec<usize>]) -> bool {
    let root = mask.trailing_zeros() as usize;
    let visited = dfs_visit(root, mask, graph, 0);
    visited.count_ones() == mask.count_ones()
}

fn dfs_visit(u: usize, mask: usize, graph: &[Vec<usize>], visited: usize) -> usize {
    let mut new_visited = visited | (1 << u);
    for &v in &graph[u] {
        let bit = 1 << v;
        if (mask & bit) != 0 && (new_visited & bit) == 0 {
            new_visited = dfs_visit(v, mask, graph, new_visited);
        }
    }
    new_visited
}

/// 递归树形 DP 计算直径
fn tree_diameter_rec(mask: usize, graph: &[Vec<usize>]) -> usize {
    fn dfs(u: usize, parent: usize, mask: usize, graph: &[Vec<usize>]) -> (usize, usize) {
        let (mut max_h, mut max_d) = (0, 0);
        let mut heights = Vec::new();

        for &v in &graph[u] {
            if v != parent && (mask >> v) & 1 == 1 {
                let (h, d) = dfs(v, u, mask, graph);
                heights.push(h);
                max_d = max_d.max(d);
            }
        }

        heights.sort_unstable_by(|a, b| b.cmp(a));
        if !heights.is_empty() {
            max_h = heights[0] + 1;
            if heights.len() >= 2 {
                max_d = max_d.max(heights[0] + heights[1] + 2);
            } else {
                max_d = max_d.max(max_h);
            }
        }
        (max_h, max_d)
    }

    let root = mask.trailing_zeros() as usize;
    dfs(root, root, mask, graph).1
}
```
