---
title: "leetcode-并查集54"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


##  带权图里旅途的最小代价

给你一个 n 个节点的带权无向图，节点编号为 0 到 n - 1 。

给你一个整数 n 和一个数组 edges ，其中 edges[i] = [ui, vi, wi] 表示节点 ui 和 vi 之间有一条权值为 wi 的无向边。

在图中，一趟旅途包含一系列节点和边。旅途开始和结束点都是图中的节点，且图中存在连接旅途中相邻节点的边。注意，一趟旅途可能访问同一条边或者同一个节点多次。

如果旅途开始于节点 u ，结束于节点 v ，我们定义这一趟旅途的 代价 是经过的边权按位与 AND 的结果。换句话说，如果经过的边对应的边权为 w0, w1, w2, ..., wk ，那么代价为w0 & w1 & w2 & ... & wk ，其中 & 表示按位与 AND 操作。

给你一个二维数组 query ，其中 query[i] = [si, ti] 。对于每一个查询，你需要找出从节点开始 si ，在节点 ti 处结束的旅途的最小代价。如果不存在这样的旅途，答案为 -1 。

返回数组 answer ，其中 answer[i] 表示对于查询 i 的 最小 旅途代价。


```
impl Solution {
    pub fn minimum_cost(n: i32, edges: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;
        let mut parent = (0..n).collect::<Vec<_>>();
        let mut rank = vec![0; n];
        let mut and_val = vec![i32::MAX; n]; // 每个连通分量的边权 AND，初始全 1

        // 查找根并路径压缩
        fn find(parent: &mut Vec<usize>, x: usize) -> usize {
            if parent[x] != x {
                parent[x] = find(parent, parent[x]);
            }
            parent[x]
        }

        // 合并两个连通分量
        fn union(
            parent: &mut Vec<usize>,
            rank: &mut Vec<usize>,
            and_val: &mut Vec<i32>,
            mut u: usize,
            mut v: usize,
            w: i32,
        ) {
            u = find(parent, u);
            v = find(parent, v);
            if u == v {
                // 同一集合，更新 AND 值（可能通过自环或重复边）
                and_val[u] &= w;
                return;
            }
            // 按秩合并
            if rank[u] < rank[v] {
                std::mem::swap(&mut u, &mut v);
            }
            parent[v] = u;
            if rank[u] == rank[v] {
                rank[u] += 1;
            }
            // 更新新根的和值：原有 AND 与当前边权 AND
            and_val[u] = and_val[u] & and_val[v] & w;
        }

        // 处理所有边
        for edge in edges {
            let u = edge[0] as usize;
            let v = edge[1] as usize;
            let w = edge[2];
            union(&mut parent, &mut rank, &mut and_val, u, v, w);
        }

        // 处理查询
        let mut ans = Vec::with_capacity(queries.len());
        for q in queries {
            let s = q[0] as usize;
            let t = q[1] as usize;
            if s == t {
                ans.push(0);
            } else {
                let rs = find(&mut parent, s);
                let rt = find(&mut parent, t);
                if rs != rt {
                    ans.push(-1);
                } else {
                    ans.push(and_val[rs]);
                }
            }
        }
        ans
    }
}
```
