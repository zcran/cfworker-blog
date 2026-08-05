---
title: "leetcode-图23"
date: 2026-07-27T10:01:36+08:00
tags: ["leetcode", "图"]
draft: false
---


## 找到离给定两个节点最近的节点

给你一个 n 个节点的 有向图 ，节点编号为 0 到 n - 1 ，每个节点 至多 有一条出边。

有向图用大小为 n 下标从 0 开始的数组 edges 表示，表示节点 i 有一条有向边指向 edges[i] 。如果节点 i 没有出边，那么 edges[i] == -1 。

同时给你两个节点 node1 和 node2 。

请你返回一个从 node1 和 node2 都能到达节点的编号，使节点 node1 和节点 node2 到这个节点的距离 较大值最小化。如果有多个答案，请返回 最小 的节点编号。如果答案不存在，返回 -1 。

注意 edges 可能包含环。


```
impl Solution {
    pub fn closest_meeting_node(edges: Vec<i32>, node1: i32, node2: i32) -> i32 {
        let n = edges.len();

        // 计算从起点 start 到每个节点的距离，-1 表示不可达
        // 由于每个节点最多一个出边，路径是唯一的，用 while 遍历即可
        fn calc_dist(edges: &[i32], start: i32) -> Vec<i32> {
            let n = edges.len();
            let mut dist = vec![-1; n];
            let mut cur = start;
            let mut step = 0;

            while cur != -1 && dist[cur as usize] == -1 {
                dist[cur as usize] = step;
                cur = edges[cur as usize];
                step += 1;
            }

            dist
        }

        let d1 = calc_dist(&edges, node1);
        let d2 = calc_dist(&edges, node2);

        let mut ans = -1;

        // 遍历所有节点，找到两个起点都能到达且最大距离最小的节点
        for i in 0..n {
            if d1[i] != -1 && d2[i] != -1 {
                let max_dist = d1[i].max(d2[i]);

                if ans == -1 || max_dist < d1[ans as usize].max(d2[ans as usize]) {
                    ans = i as i32;
                }
            }
        }

        ans
    }
}
```
