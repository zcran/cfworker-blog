---
title: "leetcode-广度优先搜索43"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 连接两棵树后最大目标节点数目 II

有两棵 无向 树，分别有 n 和 m 个树节点。两棵树中的节点编号分别为[0, n - 1] 和 [0, m - 1] 中的整数。

给你两个二维整数 edges1 和 edges2 ，长度分别为 n - 1 和 m - 1 ，其中 edges1[i] = [ai, bi] 表示第一棵树中节点 ai 和 bi 之间有一条边，edges2[i] = [ui, vi] 表示第二棵树中节点 ui 和 vi 之间有一条边。

如果节点 u 和节点 v 之间路径的边数是偶数，那么我们称节点 u 是节点 v 的 目标节点 。注意 ，一个节点一定是它自己的 目标节点 。

请你返回一个长度为 n 的整数数组 answer ，answer[i] 表示将第一棵树中的一个节点与第二棵树中的一个节点连接一条边后，第一棵树中节点 i 的 目标节点 数目的 最大值 。

注意 ，每个查询相互独立。意味着进行下一次查询之前，你需要先把刚添加的边给删掉。


```
impl Solution {
    pub fn max_target_nodes(edges1: Vec<Vec<i32>>, edges2: Vec<Vec<i32>>) -> Vec<i32> {
        // 对一棵树按深度奇偶性二分染色，返回 [偶数层节点数, 奇数层节点数]
        fn color_counts(edges: &[Vec<i32>]) -> (Vec<usize>, [i32; 2]) {
            let n = edges.len() + 1;
            let mut graph = vec![Vec::new(); n];
            for e in edges {
                let (u, v) = (e[0] as usize, e[1] as usize);
                graph[u].push(v);
                graph[v].push(u);
            }

            // 从节点 0 出发 DFS，记录每个节点的颜色（深度 % 2）
            let mut color = vec![0usize; n];
            let mut counts = [0i32; 2];

            fn dfs(
                node: usize,
                parent: isize,
                depth: usize,
                graph: &[Vec<usize>],
                color: &mut [usize],
                counts: &mut [i32; 2],
            ) {
                let c = depth % 2;
                color[node] = c;
                counts[c] += 1;
                for &next in &graph[node] {
                    if next as isize == parent {
                        continue;
                    }
                    dfs(next, node as isize, depth + 1, graph, color, counts);
                }
            }

            dfs(0, -1, 0, &graph, &mut color, &mut counts);
            (color, counts)
        }

        let (color1, counts1) = color_counts(&edges1);
        let (_, counts2) = color_counts(&edges2);

        // 第二棵树中，无论连到哪个节点，最多能贡献 max(counts2) 个同色目标节点
        let best2 = counts2[0].max(counts2[1]);

        // 对第一棵树的每个节点，答案 = 它所在颜色分量的节点数 + 第二棵树的最大贡献
        color1
            .iter()
            .map(|&c| counts1[c] + best2)
            .collect()
    }
}
```
