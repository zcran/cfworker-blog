---
title: "leetcode-树5"
date: 2026-09-24T09:47:33+08:00
tags: ["leetcode", "树"]
draft: false
---


## 边权重均等查询

现有一棵由 n 个节点组成的无向树，节点按从 0 到 n - 1 编号。给你一个整数 n 和一个长度为 n - 1 的二维整数数组 edges ，其中 edges[i] = [ui, vi, wi] 表示树中存在一条位于节点 ui 和节点 vi 之间、权重为 wi 的边。

另给你一个长度为 m 的二维整数数组 queries ，其中 queries[i] = [ai, bi] 。对于每条查询，请你找出使从 ai 到 bi 路径上每条边的权重相等所需的 最小操作次数 。在一次操作中，你可以选择树上的任意一条边，并将其权重更改为任意值。

注意：

· 查询之间 相互独立 的，这意味着每条新的查询时，树都会回到 初始状态 。
· 从 ai 到 bi的路径是一个由 不同 节点组成的序列，从节点 ai 开始，到节点 bi 结束，且序列中相邻的两个节点在树中共享一条边。

返回一个长度为 m 的数组 answer ，其中 answer[i] 是第 i 条查询的答案。


```
impl Solution {
    /// 倍增法求 LCA，并统计路径上各权重的出现次数
    ///
    /// 关键结论：对于路径 u -> v，设路径长度为 len（边数），
    /// 各权重出现次数为 cnt[0..26]（权重已映射到 0..25）。
    /// 若令路径上所有边权重相等，显然应保留出现次数最多的那种权重，
    /// 其余边全部改掉，因此最小操作次数 = len - max(cnt)。
    /// 而 len = sum(cnt)，所以答案 = sum(cnt) - max(cnt)。
    pub fn min_operations_queries(
        n: i32,
        edges: Vec<Vec<i32>>,
        queries: Vec<Vec<i32>>,
    ) -> Vec<i32> {
        let n = n as usize;

        // 建邻接表，边权映射到 0..25
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let (u, v, w) = (edge[0] as usize, edge[1] as usize, (edge[2] - 1) as usize);
            graph[u].push((v, w));
            graph[v].push((u, w));
        }

        // 倍增表所需的层数
        let log = 64 - n.leading_zeros() as usize;

        // parent[k][i]：节点 i 向上跳 2^k 步到达的节点（跳数放第一维，CPU 缓存友好）
        // depth[i]：节点 i 的深度
        // prefix[i]：从根到节点 i 路径上各权重的出现次数
        let mut parent = vec![vec![n; n]; log];
        let mut depth = vec![0usize; n];
        let mut prefix = vec![[0i32; 26]; n];

        // 迭代 DFS，避免递归深度过大
        // 栈元素：(节点, 父节点, 当前权重计数数组)
        let mut stack: Vec<(usize, usize, [i32; 26])> = vec![(0, n, [0; 26])];

        while let Some((u, fa, cur)) = stack.pop() {
            prefix[u] = cur;
            for &(v, w) in &graph[u] {
                if v == fa {
                    continue;
                }
                parent[0][v] = u;
                depth[v] = depth[u] + 1;

                // 在父节点计数基础上加上边 (u, v) 的权重
                let mut next = cur;
                next[w] += 1;
                stack.push((v, u, next));
            }
        }

        // 构建倍增表
        for k in 0..log - 1 {
            for i in 0..n {
                let mid = parent[k][i];
                if mid != n {
                    parent[k + 1][i] = parent[k][mid];
                }
            }
        }

        // 处理每条查询
        queries
            .iter()
            .map(|query| {
                let (mut u, mut v) = (query[0] as usize, query[1] as usize);

                // 保证 depth[u] <= depth[v]
                if depth[u] > depth[v] {
                    std::mem::swap(&mut u, &mut v);
                }

                // 将 v 向上跳到与 u 同深度
                let diff = depth[v] - depth[u];
                for k in 0..log {
                    if (diff >> k) & 1 == 1 {
                        v = parent[k][v];
                    }
                }

                // 若不同，继续向上跳到 LCA 的子节点
                if u != v {
                    for k in (0..log).rev() {
                        if parent[k][u] != parent[k][v] {
                            u = parent[k][u];
                            v = parent[k][v];
                        }
                    }
                    u = parent[0][u];
                }

                let lca = u;

                // 路径上各权重出现次数 = prefix[a] + prefix[b] - 2 * prefix[lca]
                let (a, b) = (query[0] as usize, query[1] as usize);
                let cnt_a = &prefix[a];
                let cnt_b = &prefix[b];
                let cnt_l = &prefix[lca];

                let mut sum = 0;
                let mut max = 0;
                for i in 0..26 {
                    let c = cnt_a[i] + cnt_b[i] - 2 * cnt_l[i];
                    sum += c;
                    max = max.max(c);
                }

                sum - max
            })
            .collect()
    }
}
```
