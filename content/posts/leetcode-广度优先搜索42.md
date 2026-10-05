---
title: "leetcode-广度优先搜索42"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 连接两棵树后最大目标节点数目 I

有两棵 无向 树，分别有 n 和 m 个树节点。两棵树中的节点编号分别为[0, n - 1] 和 [0, m - 1] 中的整数。

给你两个二维整数 edges1 和 edges2 ，长度分别为 n - 1 和 m - 1 ，其中 edges1[i] = [ai, bi] 表示第一棵树中节点 ai 和 bi 之间有一条边，edges2[i] = [ui, vi] 表示第二棵树中节点 ui 和 vi 之间有一条边。同时给你一个整数 k 。

如果节点 u 和节点 v 之间路径的边数小于等于 k ，那么我们称节点 u 是节点 v 的 目标节点 。注意 ，一个节点一定是它自己的 目标节点 。

请你返回一个长度为 n 的整数数组 answer ，answer[i] 表示将第一棵树中的一个节点与第二棵树中的一个节点连接一条边后，第一棵树中节点 i 的 目标节点 数目的 最大值 。

注意 ，每个查询相互独立。意味着进行下一次查询之前，你需要先把刚添加的边给删掉。


```
impl Solution {
    pub fn max_target_nodes(edges1: Vec<Vec<i32>>, edges2: Vec<Vec<i32>>, k: i32) -> Vec<i32> {
        // 对一棵树，求每个节点在距离 k 内可达的节点数（含自身）
        fn count_within_k(edges: &[Vec<i32>], k: i32) -> Vec<i32> {
            let n = edges.len() + 1;
            let mut graph = vec![Vec::new(); n];
            for e in edges {
                let (u, v) = (e[0] as usize, e[1] as usize);
                graph[u].push(v);
                graph[v].push(u);
            }

            // 从每个节点出发做一次深度为 k 的 DFS
            fn dfs(node: usize, parent: isize, graph: &[Vec<usize>], k: i32) -> i32 {
                if k < 0 {
                    return 0;
                }
                let mut count = 1;
                for &next in &graph[node] {
                    if next as isize == parent {
                        continue;
                    }
                    count += dfs(next, node as isize, graph, k - 1);
                }
                count
            }

            (0..n).map(|i| dfs(i, -1, &graph, k)).collect()
        }

        // 第一棵树：以每个节点为起点，距离 k 内的节点数
        let count1 = count_within_k(&edges1, k);

        // 第二棵树：以每个节点为起点，距离 k-1 内的节点数
        // 因为连接到第一棵树的某个节点时，先消耗 1 步跨树边
        let count2 = count_within_k(&edges2, k - 1);
        let best2 = count2.iter().copied().max().unwrap_or(0);

        // 对第一棵树每个节点，答案 = 自身 k 内节点数 + 第二棵树能带来的最大增量
        count1.iter().map(|&c| c + best2).collect()
    }
}
```
