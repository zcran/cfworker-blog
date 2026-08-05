---
title: "leetcode-并查集63"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 添加一个点后可激活的最大点数

给你一个二维整数数组 points，其中 points[i] = [xi, yi] 表示第 i 个点的坐标。points 中的所有坐标都 互不相同。

如果一个点被 激活，那么所有与该点具有相同 x 坐标或 y 坐标的点也会被 激活。

激活会一直持续，直到没有额外的点可以被激活为止。

你可以 额外添加 一个不在 points 数组中的整数坐标点 (x, y) 。从这个新添加的点开始 激活。

返回一个整数，表示可以被激活的 最大 点数，包括新添加的点。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_activated(points: Vec<Vec<i32>>) -> i32 {
        let n = points.len();
        if n == 0 {
            return 1; // 没有点，只能激活新增的点自身
        }

        // ---------- 并查集 ----------
        let mut parent: Vec<usize> = (0..n).collect();
        let mut rank = vec![0; n];

        // 路径压缩（迭代版）
        fn find(parent: &mut Vec<usize>, mut x: usize) -> usize {
            while parent[x] != x {
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            x
        }

        // 按秩合并
        fn union(parent: &mut Vec<usize>, rank: &mut Vec<usize>, a: usize, b: usize) {
            let ra = find(parent, a);
            let rb = find(parent, b);
            if ra == rb {
                return;
            }
            if rank[ra] < rank[rb] {
                parent[ra] = rb;
            } else if rank[ra] > rank[rb] {
                parent[rb] = ra;
            } else {
                parent[rb] = ra;
                rank[ra] += 1;
            }
        }

        // ---------- 按 x 和 y 分组 ----------
        let mut x_map: HashMap<i32, usize> = HashMap::new();
        let mut y_map: HashMap<i32, usize> = HashMap::new();

        for (i, p) in points.iter().enumerate() {
            let (x, y) = (p[0], p[1]);

            // 合并同一 x 的所有点
            if let Some(&prev) = x_map.get(&x) {
                union(&mut parent, &mut rank, prev, i);
            } else {
                x_map.insert(x, i);
            }

            // 合并同一 y 的所有点
            if let Some(&prev) = y_map.get(&y) {
                union(&mut parent, &mut rank, prev, i);
            } else {
                y_map.insert(y, i);
            }
        }

        // ---------- 计算各连通分量大小 ----------
        let mut size = vec![0; n];
        for i in 0..n {
            let root = find(&mut parent, i);
            size[root] += 1;
        }

        // ---------- 找出最大的两个分量 ----------
        let (mut max1, mut max2) = (0, 0);
        for &s in &size {
            if s > max1 {
                max2 = max1;
                max1 = s;
            } else if s > max2 {
                max2 = s;
            }
        }

        // 新增一个点可以连接两个不同分量（最大 + 次大），若只有一个分量则只能 +1
        if max2 == 0 {
            max1 + 1
        } else {
            max1 + max2 + 1
        }
    }
}
```
