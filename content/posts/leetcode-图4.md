---
title: "leetcode-图4"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 树中距离之和

给定一个无向、连通的树。树中有 n 个标记为 0...n-1 的节点以及 n-1 条边 。

给定整数 n 和数组 edges ， edges[i] = [ai, bi]表示树中的节点 ai 和 bi 之间有一条边。

返回长度为 n 的数组 answer ，其中 answer[i] 是树中第 i 个节点与所有其他节点之间的距离之和。


```
impl Solution {
    pub fn sum_of_distances_in_tree(n: i32, edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for e in edges {
            let (u, v) = (e[0] as usize, e[1] as usize);
            graph[u].push(v);
            graph[v].push(u);
        }

        // size[i]: 以 i 为根的子树节点数
        // dist[i]: 节点 i 到所有节点的距离和
        let mut size = vec![1; n];
        let mut dist = vec![0; n];

        // 第一次 DFS: 计算以 0 为根时，每个子树大小和 dist[0]
        dfs(0, n, &graph, &mut size, &mut dist);

        // 第二次 DFS: 换根 DP，计算所有节点的距离和
        reroot(0, n, &graph, &size, &mut dist);

        dist
    }
}

/// 第一次深度优先搜索
/// 计算以 0 为根时：
/// - size[i]: 子树 i 的节点数
/// - dist[0]: 根节点到所有节点的距离之和
fn dfs(u: usize, parent: usize, graph: &[Vec<usize>], size: &mut [i32], dist: &mut [i32]) {
    for &v in &graph[u] {
        if v == parent { continue; }
        dfs(v, u, graph, size, dist);
        size[u] += size[v];
        dist[0] += size[v];  // 每条边对 dist[0] 的贡献 = 子树大小
    }
}

/// 第二次深度优先搜索：换根 DP
/// 当根从 u 转移到 v 时，距离和的变化量：
/// - v 子树内的节点距离减少 1（共 size[v] 个）
/// - 其他节点距离增加 1（共 n - size[v] 个）
/// 因此: dist[v] = dist[u] + n - 2 * size[v]
fn reroot(u: usize, parent: usize, graph: &[Vec<usize>], size: &[i32], dist: &mut [i32]) {
    let n = graph.len() as i32;
    for &v in &graph[u] {
        if v == parent { continue; }
        dist[v] = dist[u] + n - 2 * size[v];
        reroot(v, u, graph, size, dist);
    }
}
```
