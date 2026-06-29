---
title: "leetcode-并查集18"
date: 2026-06-25T11:22:22+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 移除最多的同行或同列石头

n 块石头放置在二维平面中的一些整数坐标点上。每个坐标点上最多只能有一块石头。

如果一块石头的 同行或者同列 上有其他石头存在，那么就可以移除这块石头。

给你一个长度为 n 的数组 stones ，其中 stones[i] = [xi, yi] 表示第 i 块石头的位置，返回 可以移除的石子 的最大数量。


```
use std::collections::HashMap;

impl Solution {
    /// 返回可以移除的石子最大数量。
    /// 等价于：石子总数 - 连通分量数（同行或同列视为连通）。
    pub fn remove_stones(stones: Vec<Vec<i32>>) -> i32 {
        let mut uf = UnionFind::new();
        for s in &stones {
            // 将行和列映射到不同区间，避免冲突：列偏移 10001（大于坐标范围）
            let row = s[0] as usize;
            let col = (s[1] + 10001) as usize;
            uf.union(row, col);
        }
        stones.len() as i32 - uf.count
    }
}

/// 并查集（哈希表实现，支持动态加点）
struct UnionFind {
    parent: HashMap<usize, usize>, // 父节点映射
    rank: HashMap<usize, u32>,     // 按秩合并优化
    count: i32,                    // 连通分量数量
}

impl UnionFind {
    fn new() -> Self {
        Self {
            parent: HashMap::new(),
            rank: HashMap::new(),
            count: 0,
        }
    }

    /// 查找根节点（路径压缩）
    fn find(&mut self, x: usize) -> usize {
        if !self.parent.contains_key(&x) {
            // 首次出现：新建节点，自成一派
            self.parent.insert(x, x);
            self.rank.insert(x, 0);
            self.count += 1;
            return x;
        }

        // 路径压缩：递归寻找根，并扁平化
        let p = *self.parent.get(&x).unwrap();
        if p != x {
            let root = self.find(p);
            self.parent.insert(x, root);
        }
        *self.parent.get(&x).unwrap()
    }

    /// 合并两个节点所在集合
    fn union(&mut self, a: usize, b: usize) {
        let ra = self.find(a);
        let rb = self.find(b);
        if ra == rb {
            return;
        }

        // 按秩合并：将矮树接到高树下
        let (rank_a, rank_b) = (self.rank[&ra], self.rank[&rb]);
        if rank_a < rank_b {
            self.parent.insert(ra, rb);
        } else if rank_a > rank_b {
            self.parent.insert(rb, ra);
        } else {
            self.parent.insert(rb, ra);
            self.rank.insert(ra, rank_a + 1);
        }
        self.count -= 1;
    }
}
```
