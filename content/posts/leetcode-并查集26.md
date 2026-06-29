---
title: "leetcode-并查集26"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 连通网络的操作次数

用以太网线缆将 n 台计算机连接成一个网络，计算机的编号从 0 到 n-1。线缆用 connections 表示，其中 connections[i] = [a, b] 连接了计算机 a 和 b。

网络中的任何一台计算机都可以通过网络直接或者间接访问同一个网络中其他任意一台计算机。

给你这个计算机网络的初始布线 connections，你可以拔开任意两台直连计算机之间的线缆，并用它连接一对未直连的计算机。请你计算并返回使所有计算机都连通所需的最少操作次数。如果不可能，则返回 -1 。




```
impl Solution {
    /// 返回使所有计算机连通所需的最少操作次数。
    /// 操作次数 = 连通分量数 - 1（每操作一次减少一个分量）。
    /// 若线缆数 < n-1，则不可能连通。
    pub fn make_connected(n: i32, connections: Vec<Vec<i32>>) -> i32 {
        let n = n as usize;
        let m = connections.len();

        // 如果线缆少于 n-1，无法连通所有计算机
        if m < n - 1 {
            return -1;
        }

        // 并查集
        let mut parent = (0..n).collect::<Vec<_>>();
        let mut rank = vec![0; n];
        let mut components = n as i32;

        /// 查找根节点（路径压缩）
        fn find(parent: &mut Vec<usize>, mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        /// 合并两个集合（按秩合并）
        fn union(parent: &mut Vec<usize>, rank: &mut Vec<usize>, a: usize, b: usize) -> bool {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                return false; // 已连通，该线缆冗余
            }
            if rank[ra] < rank[rb] {
                parent[ra] = rb;
            } else if rank[ra] > rank[rb] {
                parent[rb] = ra;
            } else {
                parent[rb] = ra;
                rank[ra] += 1;
            }
            true
        }

        // 合并所有线缆，统计连通分量数
        for conn in connections {
            let a = conn[0] as usize;
            let b = conn[1] as usize;
            if union(&mut parent, &mut rank, a, b) {
                components -= 1;
            }
        }

        // 需要 components - 1 次操作将各分量连通
        components - 1
    }
}
```
