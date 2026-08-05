---
title: "leetcode-计数31"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 子树中标签相同的节点数

给你一棵树（即，一个连通的无环无向图），这棵树由编号从 0  到 n - 1 的 n 个节点组成，且恰好有 n - 1 条 edges 。树的根节点为节点 0 ，树上的每一个节点都有一个标签，也就是字符串 labels 中的一个小写字符（编号为 i 的 节点的标签就是 labels[i] ）

边数组 edges 以 edges[i] = [ai, bi] 的形式给出，该格式表示节点 ai 和 bi 之间存在一条边。

返回一个大小为 n 的数组，其中 ans[i] 表示第 i 个节点的子树中与节点 i 标签相同的节点数。

树 T 中的子树是由 T 中的某个节点及其所有后代节点组成的树。


```
impl Solution {
    pub fn count_sub_trees(n: i32, edges: Vec<Vec<i32>>, labels: String) -> Vec<i32> {
        let n = n as usize;
        let labels = labels.as_bytes();

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in edges {
            let (a, b) = (edge[0] as usize, edge[1] as usize);
            graph[a].push(b);
            graph[b].push(a);
        }

        // 结果数组
        let mut ans = vec![0; n];
        // 每个节点子树中各字母计数，用二维数组（栈上分配，性能更好）
        let mut count = vec![[0; 26]; n];

        // DFS：返回当前节点子树中各字母的计数（借用可变引用避免拷贝）
        fn dfs(
            node: usize,
            parent: usize,
            graph: &[Vec<usize>],
            labels: &[u8],
            count: &mut Vec<[i32; 26]>,
            ans: &mut [i32]
        ) {
            let label_idx = (labels[node] - b'a') as usize;
            count[node][label_idx] = 1;

            for &child in &graph[node] {
                if child == parent { continue; }
                dfs(child, node, graph, labels, count, ans);
                // 合并子树的计数
                for i in 0..26 {
                    count[node][i] += count[child][i];
                }
            }

            // 记录答案：当前节点子树中与其标签相同的节点数
            ans[node] = count[node][label_idx];
        }

        dfs(0, usize::MAX, &graph, labels, &mut count, &mut ans);
        ans
    }
}
```
