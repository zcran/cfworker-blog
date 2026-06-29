---
title: "leetcode-有序集合30"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 新增道路查询后的最短距离 II

给你一个整数 n 和一个二维整数数组 queries。

有 n 个城市，编号从 0 到 n - 1。初始时，每个城市 i 都有一条单向道路通往城市 i + 1（ 0 <= i < n - 1）。

queries[i] = [ui, vi] 表示新建一条从城市 ui 到城市 vi 的单向道路。每次查询后，你需要找到从城市 0 到城市 n - 1 的最短路径的长度。

所有查询中不会存在两个查询都满足 queries[i][0] < queries[j][0] < queries[i][1] < queries[j][1]。

返回一个数组 answer，对于范围 [0, queries.length - 1] 中的每个 i，answer[i] 是处理完前 i + 1 个查询后，从城市 0 到城市 n - 1 的最短路径的长度。


```
impl Solution {
    pub fn shortest_distance_after_queries(n: i32, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let n = n as usize;
        // next[i] 表示从城市 i 出发的当前最短路径的下一个城市
        // 初始时，每个城市 i 指向 i+1
        let mut next: Vec<usize> = (1..=n).collect(); // next[n-1] = n 作为哨兵
        let mut ans = Vec::with_capacity(queries.len());
        let mut dist = (n - 1) as i32;

        for q in queries {
            let u = q[0] as usize;
            let v = q[1] as usize;

            // 如果 u 的下一跳已经指向更远的城市，则无需更新
            let mut k = next[u];
            while k < v {
                // 跳过被覆盖的中间城市，直接连接到 v
                let nxt = next[k];
                next[k] = v; // 标记中间节点已被跳过
                k = nxt;
                dist -= 1;
            }

            // 更新 u 的下一跳为 v 和原本更小值中的较远者
            if next[u] < v {
                next[u] = v;
            }

            ans.push(dist);
        }

        ans
    }
}
```
