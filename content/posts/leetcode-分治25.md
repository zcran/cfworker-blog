---
title: "leetcode-分治25"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---



## 删除所有值为某个元素后的最大子数组和

给你一个整数数组 nums 。

你可以对数组执行以下操作 至多 一次：

· 选择 nums 中存在的 任意 整数 X ，确保删除所有值为 X 的元素后剩下数组 非空 。
· 将数组中 所有 值为 X 的元素都删除。

请你返回 所有 可能得到的数组中 最大 子数组 和为多少。


```
use std::collections::HashMap;

/// 线段树节点信息
#[derive(Debug, Clone, Copy)]
struct NodeInfo {
    total: i64,      // 区间总和
    max_pre: i64,    // 最大前缀和
    max_suf: i64,    // 最大后缀和
    max_sub: i64,    // 最大子数组和
}

impl NodeInfo {
    /// 从单个值创建节点
    fn from_value(val: i64) -> Self {
        Self {
            total: val,
            max_pre: val,
            max_suf: val,
            max_sub: val,
        }
    }

    /// 合并两个相邻区间
    fn merge(left: &Self, right: &Self) -> Self {
        Self {
            total: left.total + right.total,
            max_pre: left.max_pre.max(left.total + right.max_pre),
            max_suf: right.max_suf.max(right.total + left.max_suf),
            max_sub: left
                .max_sub
                .max(right.max_sub)
                .max(left.max_suf + right.max_pre),
        }
    }
}

/// 线段树，支持单点更新和全局最大子数组和查询
struct SegmentTree {
    tree: Vec<NodeInfo>,
    n: usize,
}

impl SegmentTree {
    /// 从数组构建线段树
    fn new(nums: &[i32]) -> Self {
        let n = nums.len();
        // 线段树大小：4*n 足够
        let mut tree = vec![NodeInfo::from_value(0); 4 * n];
        let mut seg = Self { tree, n };
        seg.build(nums, 1, 0, n - 1);
        seg
    }

    /// 递归构建线段树
    fn build(&mut self, nums: &[i32], node: usize, l: usize, r: usize) {
        if l == r {
            self.tree[node] = NodeInfo::from_value(nums[l] as i64);
            return;
        }
        let mid = (l + r) / 2;
        self.build(nums, node * 2, l, mid);
        self.build(nums, node * 2 + 1, mid + 1, r);
        self.push_up(node);
    }

    /// 单点更新：将位置 pos 的值设为 val
    fn update(&mut self, pos: usize, val: i32) {
        self.update_recursive(1, 0, self.n - 1, pos, val as i64);
    }

    /// 递归更新
    fn update_recursive(&mut self, node: usize, l: usize, r: usize, pos: usize, val: i64) {
        if l == r {
            self.tree[node] = NodeInfo::from_value(val);
            return;
        }
        let mid = (l + r) / 2;
        if pos <= mid {
            self.update_recursive(node * 2, l, mid, pos, val);
        } else {
            self.update_recursive(node * 2 + 1, mid + 1, r, pos, val);
        }
        self.push_up(node);
    }

    /// 向上更新父节点
    #[inline(always)]
    fn push_up(&mut self, node: usize) {
        self.tree[node] = NodeInfo::merge(&self.tree[node * 2], &self.tree[node * 2 + 1]);
    }

    /// 获取整个数组的最大子数组和
    fn max_subarray_sum(&self) -> i64 {
        self.tree[1].max_sub
    }
}

impl Solution {
    /// 计算至多删除一种负数后，数组的最大子数组和
    ///
    /// # 算法思路
    /// 1. 首先计算不删除任何数时的最大子数组和
    /// 2. 如果最大和 <= 0，直接返回（删除负数只会让和更小）
    /// 3. 否则，尝试删除每种负数，计算删除后的最大子数组和
    /// 4. 返回所有情况中的最大值
    ///
    /// # 复杂度
    /// - 时间复杂度：O(n log n + m * k * log n)，m 是负数种类数，k 是每种负数的出现次数
    /// - 空间复杂度：O(n)
    pub fn max_subarray_sum(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        let mut seg_tree = SegmentTree::new(&nums);

        // 情况1：不删除任何数
        let mut max_sum = seg_tree.max_subarray_sum();
        if max_sum <= 0 {
            return max_sum;
        }

        // 收集所有负数及其出现位置（值相同的负数归为一组）
        let mut neg_positions: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, &val) in nums.iter().enumerate() {
            if val < 0 {
                neg_positions.entry(val).or_insert_with(Vec::new).push(i);
            }
        }

        // 尝试删除每种负数
        for (_, positions) in neg_positions.iter() {
            // 删除该种负数的所有出现（置为 0）
            for &pos in positions {
                seg_tree.update(pos, 0);
            }

            // 更新最大和
            max_sum = max_sum.max(seg_tree.max_subarray_sum());

            // 恢复原状，以便测试下一种负数
            for &pos in positions {
                seg_tree.update(pos, nums[pos]);
            }
        }

        max_sum
    }
}
```
