---
title: "leetcode-数论40"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 统计树中的合法路径数目

给你一棵 n 个节点的无向树，节点编号为 1 到 n 。给你一个整数 n 和一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ui, vi] 表示节点 ui 和 vi 在树中有一条边。

请你返回树中的 合法路径数目 。

如果在节点 a 到节点 b 之间 恰好有一个 节点的编号是质数，那么我们称路径 (a, b) 是 合法的 。

注意：

路径 (a, b) 指的是一条从节点 a 开始到节点 b 结束的一个节点序列，序列中的节点 互不相同 ，且相邻节点之间在树上有一条边。

路径 (a, b) 和路径 (b, a) 视为 同一条 路径，且只计入答案 一次 。


```
use std::collections::VecDeque;

impl Solution {
    /// 计算树中合法路径数量（路径上恰好有一个质数节点）
    ///
    /// 核心思路：
    /// 1. 将所有质数节点视为"分隔点"，它们将树分割成多个非质数连通分量
    /// 2. 对于每个质数节点，统计其相邻非质数分量的大小
    /// 3. 合法路径由质数节点 + 两个不同分量中的节点组成，或质数节点 + 一个分量中的节点
    pub fn count_paths(n: i32, edges: Vec<Vec<i32>>) -> i64 {
        let n = n as usize;
        let mut graph = vec![vec![]; n + 1];

        // 构建图
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // 埃氏筛标记质数
        let is_prime = sieve(n);

        // 只保留非质数节点的边（移除质数节点作为连接）
        let mut tree = vec![vec![]; n + 1];
        for u in 1..=n {
            if !is_prime[u] {
                for &v in &graph[u] {
                    if !is_prime[v] {
                        tree[u].push(v);
                    }
                }
            }
        }

        // 计算每个非质数节点所在分量的大小
        let mut comp_size = vec![0; n + 1];
        let mut visited = vec![false; n + 1];

        for i in 1..=n {
            if !is_prime[i] && !visited[i] {
                let size = bfs_component(&tree, i, &mut visited);
                // 将该分量大小标记到所有节点上
                mark_component(&mut comp_size, &tree, i, size);
            }
        }

        // 遍历每个质数节点，计算经过它的合法路径
        let mut ans = 0;
        for p in 1..=n {
            if is_prime[p] {
                let mut sum = 0;
                for &neighbor in &graph[p] {
                    if !is_prime[neighbor] {
                        let size = comp_size[neighbor];
                        // 当前质数节点到该分量任意节点的路径
                        ans += sum * size;
                        ans += size; // 质数节点到分量节点的路径
                        sum += size;
                    }
                }
            }
        }

        ans
    }
}

/// 埃氏筛计算质数
fn sieve(n: usize) -> Vec<bool> {
    let mut is_prime = vec![true; n + 1];
    if n >= 0 {
        is_prime[0] = false;
    }
    if n >= 1 {
        is_prime[1] = false;
    }

    let limit = (n as f64).sqrt() as usize;
    for i in 2..=limit {
        if is_prime[i] {
            let mut j = i * i;
            while j <= n {
                is_prime[j] = false;
                j += i;
            }
        }
    }

    is_prime
}

/// BFS 计算连通分量大小
fn bfs_component(graph: &[Vec<usize>], start: usize, visited: &mut [bool]) -> i64 {
    let mut queue = VecDeque::new();
    queue.push_back(start);
    visited[start] = true;
    let mut size = 0;

    while let Some(node) = queue.pop_front() {
        size += 1;
        for &neighbor in &graph[node] {
            if !visited[neighbor] {
                visited[neighbor] = true;
                queue.push_back(neighbor);
            }
        }
    }

    size
}

/// 将分量大小标记到该分量的所有节点
fn mark_component(comp_size: &mut [i64], graph: &[Vec<usize>], start: usize, size: i64) {
    let mut stack = vec![start];
    let mut visited = vec![false; graph.len()];
    visited[start] = true;

    while let Some(node) = stack.pop() {
        comp_size[node] = size;
        for &neighbor in &graph[node] {
            if !visited[neighbor] {
                visited[neighbor] = true;
                stack.push(neighbor);
            }
        }
    }
}
```
