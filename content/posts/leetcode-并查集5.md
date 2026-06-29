---
title: "leetcode-并查集5"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 冗余连接

树可以看成是一个连通且 无环 的 无向 图。

给定一个图，该图从一棵 n 个节点 (节点值 1～n) 的树中添加一条边后获得。添加的边的两个不同顶点编号在 1 到 n 中间，且这条附加的边不属于树中已存在的边。图的信息记录于长度为 n 的二维数组 edges ，edges[i] = [ai, bi] 表示图中在 ai 和 bi 之间存在一条边。

请找出一条可以删去的边，删除后可使得剩余部分是一个有着 n 个节点的树。如果有多个答案，则返回数组 edges 中最后出现的那个。


```
impl Solution {
    pub fn find_redundant_connection(edges: Vec<Vec<i32>>) -> Vec<i32> {
        let n = edges.len();
        let mut parent = (0..=n).collect::<Vec<_>>(); // 父节点数组，parent[i] 表示 i 的父节点
        let mut size = vec![1; n + 1]; // 集合大小，用于按秩合并

        /// 查找根节点，带路径压缩
        fn find(parent: &mut [usize], mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]]; // 路径压缩
                x = parent[x];
            }
            x
        }

        /// 合并两个节点所在的集合，返回是否成功合并
        /// 如果已经在同一集合，说明会形成环，返回 false
        fn union(parent: &mut [usize], size: &mut [usize], a: usize, b: usize) -> bool {
            let ra = find(parent, a);
            let rb = find(parent, b);

            if ra == rb {
                return false; // 已连通，添加此边会形成环
            }

            // 按秩合并：将较小的树挂到较大的树下
            if size[ra] < size[rb] {
                parent[ra] = rb;
                size[rb] += size[ra];
            } else {
                parent[rb] = ra;
                size[ra] += size[rb];
            }
            true
        }

        // 依次尝试添加每条边，首次失败的边即为答案
        for edge in edges {
            let a = edge[0] as usize;
            let b = edge[1] as usize;
            if !union(&mut parent, &mut size, a, b) {
                return edge; // 直接返回该边
            }
        }

        vec![] // 理论上不会执行到这里
    }
}
```
