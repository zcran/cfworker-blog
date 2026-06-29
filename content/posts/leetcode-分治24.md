---
title: "leetcode-分治24"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 不包含相邻元素的子序列的最大和

给你一个整数数组 nums 和一个二维数组 queries，其中 queries[i] = [posi, xi]。

对于每个查询 i，首先将 nums[posi] 设置为 xi，然后计算查询 i 的答案，该答案为 nums 中 不包含相邻元素 的 子序列 的 最大 和。

返回所有查询的答案之和。

由于最终答案可能非常大，返回其对 10^9 + 7 取余 的结果。

子序列 是指从另一个数组中删除一些或不删除元素而不改变剩余元素顺序得到的数组。


```
const MOD: i64 = 1_000_000_007;

/// 线段树节点，存储区间内四种状态的最大和
///
/// 状态定义（x 表示左端点选择情况，y 表示右端点选择情况）：
/// - v00: 左右端点都不选时的最大和
/// - v01: 左端点不选、右端点选时的最大和
/// - v10: 左端点选、右端点不选时的最大和
/// - v11: 左右端点都选时的最大和
#[derive(Clone, Copy)]
struct Node {
    v00: i64,
    v01: i64,
    v10: i64,
    v11: i64,
}

impl Node {
    /// 创建叶子节点（单个元素）
    fn from_value(val: i64) -> Self {
        Self {
            v00: 0,
            v01: 0,
            v10: 0,
            v11: val.max(0),  // 只能选自己，且负数不如不选
        }
    }

    /// 合并两个相邻区间
    fn merge(left: &Self, right: &Self) -> Self {
        Self {
            // 左不右不：左(00) + 右(10) 或 左(01) + 右(00)
            v00: (left.v00 + right.v10).max(left.v01 + right.v00),
            // 左不右选：左(00) + 右(11) 或 左(01) + 右(01)
            v01: (left.v00 + right.v11).max(left.v01 + right.v01),
            // 左选右不：左(10) + 右(10) 或 左(11) + 右(00)
            v10: (left.v10 + right.v10).max(left.v11 + right.v00),
            // 左选右选：左(10) + 右(11) 或 左(11) + 右(01)
            v11: (left.v10 + right.v11).max(left.v11 + right.v01),
        }
    }

    /// 获取当前区间的最大子序列和（允许不选任何元素）
    fn max_sum(&self) -> i64 {
        self.v11
    }
}

/// 支持单点更新、区间查询的线段树
struct SegTree {
    size: usize,
    tree: Vec<Node>,
}

impl SegTree {
    /// 创建新线段树
    fn new(n: usize) -> Self {
        // 线段树大小：4*n 足够
        let tree = vec![Node { v00: 0, v01: 0, v10: 0, v11: 0 }; n * 4];
        Self { size: n, tree }
    }

    /// 从数组构建线段树
    fn build(&mut self, nums: &[i32]) {
        Self::build_recursive(&mut self.tree, 1, 0, self.size - 1, nums);
    }

    /// 递归构建
    fn build_recursive(tree: &mut [Node], idx: usize, l: usize, r: usize, nums: &[i32]) {
        if l == r {
            tree[idx] = Node::from_value(nums[l] as i64);
            return;
        }
        let mid = (l + r) / 2;
        Self::build_recursive(tree, idx * 2, l, mid, nums);
        Self::build_recursive(tree, idx * 2 + 1, mid + 1, r, nums);
        tree[idx] = Node::merge(&tree[idx * 2], &tree[idx * 2 + 1]);
    }

    /// 单点更新
    fn update(&mut self, pos: usize, val: i32) {
        Self::update_recursive(&mut self.tree, 1, 0, self.size - 1, pos, val as i64);
    }

    /// 递归更新
    fn update_recursive(tree: &mut [Node], idx: usize, l: usize, r: usize, pos: usize, val: i64) {
        if l == r {
            tree[idx] = Node::from_value(val);
            return;
        }
        let mid = (l + r) / 2;
        if pos <= mid {
            Self::update_recursive(tree, idx * 2, l, mid, pos, val);
        } else {
            Self::update_recursive(tree, idx * 2 + 1, mid + 1, r, pos, val);
        }
        tree[idx] = Node::merge(&tree[idx * 2], &tree[idx * 2 + 1]);
    }

    /// 获取整个数组的最大子序列和
    fn max_sum(&self) -> i64 {
        self.tree[1].max_sum()
    }
}

impl Solution {
    /// 计算所有查询后，不包含相邻元素的子序列的最大和的总和
    ///
    /// # 参数
    /// - `nums`: 初始数组
    /// - `queries`: 查询数组，每个查询 [pos, val] 表示将 nums[pos] 设置为 val
    ///
    /// # 返回
    /// - 所有查询结果之和 mod 1e9+7
    ///
    /// # 算法思路
    /// 1. 使用线段树维护区间内四种状态的最大和
    /// 2. 单点更新后，根节点存储整个数组的最大子序列和
    /// 3. 累加每次查询后的结果
    pub fn maximum_sum_subsequence(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();
        let mut seg_tree = SegTree::new(n);
        seg_tree.build(&nums);

        let mut total = 0;
        for q in queries {
            let pos = q[0] as usize;
            let val = q[1];
            seg_tree.update(pos, val);
            total = (total + seg_tree.max_sum()) % MOD;
        }
        total as i32
    }
}
```
