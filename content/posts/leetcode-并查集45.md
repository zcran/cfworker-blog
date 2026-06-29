---
title: "leetcode-并查集45"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 好路径的数目

给你一棵 n 个节点的树（连通无向无环的图），节点编号从 0 到 n - 1 且恰好有 n - 1 条边。

给你一个长度为 n 下标从 0 开始的整数数组 vals ，分别表示每个节点的值。同时给你一个二维整数数组 edges ，其中 edges[i] = [ai, bi] 表示节点 ai 和 bi 之间有一条 无向 边。

一条 好路径 需要满足以下条件：

1.开始节点和结束节点的值 相同 。
2.开始节点和结束节点中间的所有节点值都 小于等于 开始节点的值（也就是说开始节点的值应该是路径上所有节点的最大值）。

请你返回不同好路径的数目。

注意，一条路径和它反向的路径算作 同一 路径。比方说， 0 -> 1 与 1 -> 0 视为同一条路径。单个节点也视为一条合法路径。


```
impl Solution {
    pub fn number_of_good_paths(vals: Vec<i32>, edges: Vec<Vec<i32>>) -> i32 {
        let n = vals.len();

        // 构建邻接表
        let mut graph = vec![Vec::new(); n];
        for edge in &edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            graph[u].push(v);
            graph[v].push(u);
        }

        // 按节点值升序排序
        let mut nodes: Vec<usize> = (0..n).collect();
        nodes.sort_by_key(|&i| vals[i]);

        // 并查集
        let mut parent = (0..n).collect::<Vec<_>>();
        let mut size = vec![1; n];

        fn find(parent: &mut [usize], x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        let mut ans = n as i32; // 每个单节点都是一条好路径

        // 按值从小到大处理节点
        for &x in &nodes {
            let vx = vals[x];
            let root_x = find(&mut parent, x);

            for &y in &graph[x] {
                let root_y = find(&mut parent, y);
                if root_y == root_x || vals[root_y] > vx {
                    continue;
                }

                // 如果两个连通块的值相等，则它们之间可以形成好路径
                if vals[root_y] == vx {
                    ans += size[root_x] * size[root_y];
                    size[root_x] += size[root_y];
                }

                // 合并连通块
                parent[root_y] = root_x;
            }
        }

        ans
    }
}
```
