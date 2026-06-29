---
title: "leetcode-线段树10"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 求出数组的 X 值 II

给你一个由 正整数 组成的数组 nums 和一个 正整数 k。同时给你一个二维数组 queries，其中 queries[i] = [indexi, valuei, starti, xi]。

你可以对 nums 执行 一次 操作，移除 nums 的任意 后缀 ，使得 nums 仍然非空。

给定一个 x，nums 的 x值 定义为执行以上操作后剩余元素的 乘积 除以 k 的 余数 为 x 的方案数。

对于 queries 中的每个查询，你需要执行以下操作，然后确定 xi 对应的 nums 的 x值：

· 将 nums[indexi] 更新为 valuei。仅这个更改在接下来的所有查询中保留。
· 移除 前缀 nums[0..(starti - 1)]（nums[0..(-1)] 表示 空前缀 ）。

返回一个长度为 queries.length 的数组 result，其中 result[i] 是第 i 个查询的答案。

数组的一个 前缀 是从数组开始位置到任意位置的子数组。

数组的一个 后缀 是从数组中任意位置开始直到结束的子数组。

子数组 是数组中一段连续的元素序列。

注意：操作中所选的前缀或后缀可以是 空的 。

注意：x值在本题中与问题 I 有不同的定义。


```
use std::collections::HashMap;

/// 线段树节点存储：
/// - mul: 区间内所有元素乘积对 k 取模
/// - cnt: 长度为 k 的数组，cnt[r] 表示区间内前缀乘积余数为 r 的方案数
type Node = (i32, [i32; 5]);

/// 线段树，支持单点更新和区间查询
struct SegmentTree {
    n: usize,
    k: usize,
    tree: Vec<Node>,
}

impl SegmentTree {
    /// 用数组 a 和模数 k 构建线段树
    fn new(a: &[i32], k: usize) -> Self {
        let n = a.len();
        let mut tree = vec![(0, [0; 5]); 4 * n + 4];
        let mut seg = SegmentTree { n, k, tree };
        seg.build(a, 1, 0, n - 1);
        seg
    }

    /// 构建线段树
    fn build(&mut self, a: &[i32], node: usize, l: usize, r: usize) {
        if l == r {
            self.tree[node] = Self::new_node(a[l], self.k);
            return;
        }
        let mid = (l + r) >> 1;
        self.build(a, node << 1, l, mid);
        self.build(a, node << 1 | 1, mid + 1, r);
        self.tree[node] = self.merge(self.tree[node << 1], self.tree[node << 1 | 1]);
    }

    /// 创建叶子节点
    #[inline]
    fn new_node(val: i32, k: usize) -> Node {
        let mul = val % k as i32;
        let mut cnt = [0; 5];
        cnt[mul as usize] = 1;
        (mul, cnt)
    }

    /// 合并左右子节点
    /// 左区间 L 和右区间 R 合并：
    /// 1. L 的所有前缀方案直接继承
    /// 2. 跨越两个区间的前缀 = L的完整乘积 × R的每个前缀方案
    #[inline]
    fn merge(&self, left: Node, right: Node) -> Node {
        let (left_mul, left_cnt) = left;
        let (right_mul, right_cnt) = right;
        let mut cnt = [0; 5];
        let k = self.k as i32;

        // L 的所有前缀方案
        for r in 0..self.k {
            cnt[r] += left_cnt[r];
        }

        // 跨越两个区间的前缀：L的完整乘积 × R的每个前缀方案
        for r in 0..self.k {
            if right_cnt[r] > 0 {
                let new_r = (left_mul * r as i32) % k;
                cnt[new_r as usize] += right_cnt[r];
            }
        }

        let mul = (left_mul * right_mul) % k;
        (mul, cnt)
    }

    /// 单点更新
    fn update(&mut self, idx: usize, val: i32) {
        self.update_rec(1, 0, self.n - 1, idx, val);
    }

    fn update_rec(&mut self, node: usize, l: usize, r: usize, idx: usize, val: i32) {
        if l == r {
            self.tree[node] = Self::new_node(val, self.k);
            return;
        }
        let mid = (l + r) >> 1;
        if idx <= mid {
            self.update_rec(node << 1, l, mid, idx, val);
        } else {
            self.update_rec(node << 1 | 1, mid + 1, r, idx, val);
        }
        self.tree[node] = self.merge(self.tree[node << 1], self.tree[node << 1 | 1]);
    }

    /// 区间查询 [ql, qr]
    fn query(&self, ql: usize, qr: usize) -> Node {
        self.query_rec(1, 0, self.n - 1, ql, qr)
    }

    fn query_rec(&self, node: usize, l: usize, r: usize, ql: usize, qr: usize) -> Node {
        if ql <= l && r <= qr {
            return self.tree[node];
        }
        let mid = (l + r) >> 1;
        if qr <= mid {
            return self.query_rec(node << 1, l, mid, ql, qr);
        }
        if ql > mid {
            return self.query_rec(node << 1 | 1, mid + 1, r, ql, qr);
        }
        let left = self.query_rec(node << 1, l, mid, ql, qr);
        let right = self.query_rec(node << 1 | 1, mid + 1, r, ql, qr);
        self.merge(left, right)
    }
}

impl Solution {
    /// 处理查询，返回每个查询的答案
    ///
    /// 思路：
    /// 1. 使用线段树维护数组 nums 的前缀信息
    /// 2. 每个节点存储：
    ///    - mul: 区间内所有元素的乘积对 k 取模
    ///    - cnt[i]: 区间内前缀乘积余数为 i 的方案数
    /// 3. 查询时，计算区间 [start, n-1] 内的 cnt[x]，即为答案
    pub fn result_array(nums: Vec<i32>, k: i32, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let k = k as usize;
        let mut seg = SegmentTree::new(&nums, k);
        let n = nums.len();

        let mut ans = Vec::with_capacity(queries.len());
        for q in &queries {
            let idx = q[0] as usize;
            let val = q[1];
            let start = q[2] as usize;
            let x = q[3] as usize;

            // 更新 nums[idx] = val
            seg.update(idx, val);

            // 查询后缀 [start, n-1]
            let (_, cnt) = seg.query(start, n - 1);
            ans.push(cnt[x]);
        }

        ans
    }
}
```
