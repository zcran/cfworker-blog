---
title: "Leetcode 最小生成树1"
date: 2023-12-19T15:44:58+08:00
tags: ["leetcode", "最小生成树"]
draft: false
---

## 连接所有点的最小费用

给你一个points 数组，表示 2D 平面上的一些点，其中 points[i] = [x⌄i, y⌄i] 。

连接点 [x⌄i, y⌄i] 和点 [x⌄j, y⌄j] 的费用为它们之间的 曼哈顿距离 ：|x⌄i - x⌄j| + |y⌄i - y⌄j| ，其中 |val| 表示 val 的绝对值。

请你返回将所有点连接的最小总费用。只有任意两点之间 有且仅有 一条简单路径时，才认为所有点都已连接。

```
impl Solution {
    pub fn min_cost_connect_points(points: Vec<Vec<i32>>) -> i32 {
        let n = points.len();
        let mut dist = vec![i32::MAX; n];
        let mut edges = vec![];

        Self::build_edges(&mut edges, points.iter().map(|x| (x[0], x[1])).collect());
        Self::build_edges(&mut edges, points.iter().map(|x| (x[1], x[0])).collect());
        Self::build_edges(&mut edges, points.iter().map(|x| (-x[1], x[0])).collect());
        Self::build_edges(&mut edges, points.iter().map(|x| (x[0], -x[1])).collect());

        let mut dsu = DSU::new(n);
        let mut ans = 0;
        edges.sort();
        // println!("{:?}", edges);
        for (d, i, j) in edges {
            if !dsu.connected(i, j) {
                ans += d;
                dsu.unite(i, j);
            }
        }
        ans
    }

    fn build_edges(edges: &mut Vec<(i32, usize, usize)>, points: Vec<(i32, i32)>) {
        let mut points_i: Vec<_> = points.iter().enumerate().map(|(i, &x)| (x, i)).collect();
        points_i.sort();

        let a: std::collections::BTreeSet<_> = points.iter().map(|x| x.1 - x.0).collect();
        let b: Vec<_> = a.into_iter().collect();

        let num = b.len();
        let mut bit = BIT::new(num);

        for ((x, y), i) in points_i.into_iter().rev() {
            let val = y - x;
            let rank = b.binary_search(&val).unwrap();
            // println!("idx: {}, val: {}", idx, val);
            if let Some(j) = bit.query(rank) {
                let d = (x - points[j].0).abs() + (y - points[j].1).abs();
                edges.push((d, i, j));
            }
            bit.update(x + y, rank, i);
        }
    }
}

struct BIT {
    tree: Vec<i32>,
    indices: Vec<Option<usize>>,
}

impl BIT {
    fn new(n: usize) -> Self {
        Self {
            tree: vec![i32::MAX; n + 1],
            indices: vec![None; n + 1],
        }
    }

    fn lowbit(x: usize) -> usize {
        debug_assert!(x > 0);
        x - (x & (x - 1))
    }

    fn update(&mut self, val: i32, rank: usize, idx: usize) {
        let mut i = rank + 1;
        while i > 0 {
            // println!("in update");
            if self.tree[i] > val {
                self.tree[i] = val;
                self.indices[i].replace(idx);
            }
            i -= Self::lowbit(i);
        }
    }

    fn query(&self, rank: usize) -> Option<usize> {
        let mut ans = None;
        let mut min_val = i32::MAX;
        let mut i = rank + 1;

        while i < self.tree.len() {
            // println!("in query i: {}", i);
            if min_val > self.tree[i] {
                min_val = self.tree[i];
                ans = self.indices[i];
            }
            i += Self::lowbit(i);
        }
        ans
    }
}

struct DSU {
    par: Vec<usize>,
    sz: Vec<usize>,
}

impl DSU {
    fn new(n: usize) -> Self {
        Self {
            par: (0..n).collect(),
            sz: vec![0; n],
        }
    }

    fn len(&self) -> usize {
        let mut ans = 0;
        for (i, p) in self.par.iter().enumerate() {
            if i == *p {
                ans += self.sz[i];
            }
        }
        ans
    }

    fn find(&mut self, x: usize) -> usize {
        if self.par[x] != x {
            self.par[x] = self.find(self.par[x]);
        }
        self.par[x]
    }

    fn insert(&mut self, x: usize) {
        if self.sz[x] == 0 {
            self.sz[x] = 1
        }
    }

    fn unite(&mut self, x: usize, y: usize) {
        self.insert(x);
        self.insert(y);
        let (mut px, mut py) = (self.find(x), self.find(y));
        if px != py {
            if self.sz[px] > self.sz[py] {
                std::mem::swap(&mut px, &mut py);
            }
            self.par[px] = self.par[py];
            self.sz[py] += self.sz[px];
        }
    }

    fn connected(&mut self, x: usize, y: usize) -> bool {
        self.find(x) == self.find(y)
    }

    fn component_num(&self) -> usize {
        self.par
            .iter()
            .enumerate()
            .filter(|&(x, p)| self.sz[x] > 0 && x == *p)
            .count()
    }
}
```