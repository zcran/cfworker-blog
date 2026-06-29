---
title: "leetcode-数论5"
date: 2026-06-20T11:09:54+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 按公因数计算最大组件大小

给定一个由不同正整数的组成的非空数组 nums ，考虑下面的图：

有 nums.length 个节点，按从 nums[0] 到 nums[nums.length - 1] 标记；

只有当 nums[i] 和 nums[j] 共用一个大于 1 的公因数时，nums[i] 和 nums[j]之间才有一条边。

返回 图中最大连通组件的大小 。



```
use std::collections::HashMap;

/// 并查集：用于维护元素之间的连通性
struct UnionFind {
    parent: Vec<usize>,
    size: Vec<usize>,
}

impl UnionFind {
    /// 初始化并查集，每个节点独立成组
    fn new(n: usize) -> Self {
        Self {
            parent: (0..n).collect(),
            size: vec![1; n],
        }
    }

    /// 路径压缩查找：返回元素 x 的根节点
    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    /// 合并两个元素所在的集合
    fn union(&mut self, a: usize, b: usize) {
        let ra = self.find(a);
        let rb = self.find(b);
        if ra == rb {
            return;
        }

        // 按大小合并：将小树合并到大树
        if self.size[ra] < self.size[rb] {
            self.parent[ra] = rb;
            self.size[rb] += self.size[ra];
        } else {
            self.parent[rb] = ra;
            self.size[ra] += self.size[rb];
        }
    }

    /// 获取元素所在集合的大小
    fn get_size(&mut self, x: usize) -> usize {
        let root = self.find(x);
        self.size[root]
    }
}

impl Solution {
    /// 计算图中最大连通组件的大小
    ///
    /// 核心思路：
    /// 1. 对每个数，将其与它的所有质因数连接
    /// 2. 使用并查集维护连通性
    /// 3. 统计每个根节点下的元素数量
    pub fn largest_component_size(nums: Vec<i32>) -> i32 {
        // ---------- 1. 预处理：找出最大值确定并查集大小 ----------
        let max_val = *nums.iter().max().unwrap() as usize;

        // 索引 0 和 1 不使用，但保留以便于索引访问
        let mut uf = UnionFind::new(max_val + 1);

        // ---------- 2. 连接每个数与其质因数 ----------
        for &num in &nums {
            let num = num as usize;
            let sqrt = (num as f64).sqrt() as usize;

            // 从 2 到 sqrt 寻找质因数
            for i in 2..=sqrt {
                if num % i == 0 {
                    // 连接 num 与质因数 i
                    uf.union(num, i);
                    // 连接 num 与对应的另一个因数 num / i
                    uf.union(num, num / i);
                }
            }
        }

        // ---------- 3. 统计每个连通分量的大小 ----------
        let mut component_size = HashMap::new();
        let mut max_size = 0;

        for &num in &nums {
            let root = uf.find(num as usize);
            let size = component_size.entry(root).or_insert(0);
            *size += 1;
            max_size = max_size.max(*size);
        }

        max_size as i32
    }
}
```
