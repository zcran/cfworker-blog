---
title: "leetcode-并查集15"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 可能的二分法

给定一组 n 人（编号为 1, 2, ..., n）， 我们想把每个人分进任意大小的两组。每个人都可能不喜欢其他人，那么他们不应该属于同一组。

给定整数 n 和数组 dislikes ，其中 dislikes[i] = [ai, bi] ，表示不允许将编号为 ai 和  bi的人归入同一组。当可以用这种方法将所有人分进两组时，返回 true；否则返回 false。


```
impl Solution {
    pub fn possible_bipartition(n: i32, dislikes: Vec<Vec<i32>>) -> bool {
        let n = n as usize;

        // 构建邻接表
        let mut graph = vec![vec![]; n];
        for edge in dislikes {
            let (a, b) = (edge[0] as usize - 1, edge[1] as usize - 1);
            graph[a].push(b);
            graph[b].push(a);
        }

        // 染色：0=未染色，1=红色，-1=蓝色
        let mut color = vec![0; n];

        // DFS 染色
        fn dfs(node: usize, c: i32, graph: &[Vec<usize>], color: &mut [i32]) -> bool {
            color[node] = c;
            for &neighbor in &graph[node] {
                if color[neighbor] == c {
                    return false; // 相邻节点颜色相同，不是二分图
                }
                if color[neighbor] == 0 && !dfs(neighbor, -c, graph, color) {
                    return false;
                }
            }
            true
        }

        // 遍历所有节点（处理非连通图）
        for i in 0..n {
            if color[i] == 0 && !dfs(i, 1, &graph, &mut color) {
                return false;
            }
        }

        true
    }
}
```
