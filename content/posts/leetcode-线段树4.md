---
title: "leetcode-线段树4"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 更新数组后处理求和查询

给你两个下标从 0 开始的数组 nums1 和 nums2 ，和一个二维数组 queries 表示一些操作。总共有 3 种类型的操作：

1.操作类型 1 为 queries[i] = [1, l, r] 。你需要将 nums1 从下标 l 到下标 r 的所有 0 反转成 1 并且所有 1 反转成 0 。l 和 r 下标都从 0 开始。

2.操作类型 2 为 queries[i] = [2, p, 0] 。对于 0 <= i < n 中的所有下标，令 nums2[i] = nums2[i] + nums1[i] * p 。

3.操作类型 3 为 queries[i] = [3, 0, 0] 。求 nums2 中所有元素的和。

请你返回一个 数组，包含 所有第三种操作类型 的答案。


```
struct SegTree {
    /// 线段树节点，存储区间和（1的个数）及懒标记
    tree: Vec<(i32, bool)>, // (sum, lazy)
    n: usize,
}

impl SegTree {
    /// 构建线段树，叶子节点存储 nums1 中的值
    pub fn new(nums: &[i32]) -> Self {
        let n = nums.len();
        let mut tree = vec![(0, false); n * 4];
        Self::build(&mut tree, 1, 0, n - 1, nums);
        SegTree { tree, n }
    }

    fn build(tree: &mut [(i32, bool)], idx: usize, l: usize, r: usize, nums: &[i32]) {
        if l == r {
            tree[idx].0 = nums[l];
            return;
        }
        let mid = (l + r) >> 1;
        Self::build(tree, idx * 2, l, mid, nums);
        Self::build(tree, idx * 2 + 1, mid + 1, r, nums);
        tree[idx].0 = tree[idx * 2].0 + tree[idx * 2 + 1].0;
    }

    /// 下传懒标记
    #[inline]
    fn push_down(tree: &mut [(i32, bool)], idx: usize, l: usize, r: usize) {
        if !tree[idx].1 || l == r {
            return;
        }
        let mid = (l + r) >> 1;
        let left = idx * 2;
        let right = idx * 2 + 1;

        // 反转左孩子
        tree[left].0 = (mid - l + 1) as i32 - tree[left].0;
        tree[left].1 ^= true;

        // 反转右孩子
        tree[right].0 = (r - mid) as i32 - tree[right].0;
        tree[right].1 ^= true;

        tree[idx].1 = false;
    }

    /// 区间反转 [ql, qr]
    pub fn reverse(&mut self, ql: usize, qr: usize) {
        Self::reverse_range(&mut self.tree, 1, 0, self.n - 1, ql, qr);
    }

    fn reverse_range(tree: &mut [(i32, bool)], idx: usize, l: usize, r: usize, ql: usize, qr: usize) {
        if ql <= l && r <= qr {
            tree[idx].0 = (r - l + 1) as i32 - tree[idx].0;
            tree[idx].1 ^= true;
            return;
        }
        Self::push_down(tree, idx, l, r);
        let mid = (l + r) >> 1;
        if ql <= mid {
            Self::reverse_range(tree, idx * 2, l, mid, ql, qr);
        }
        if qr > mid {
            Self::reverse_range(tree, idx * 2 + 1, mid + 1, r, ql, qr);
        }
        tree[idx].0 = tree[idx * 2].0 + tree[idx * 2 + 1].0;
    }

    /// 查询整个数组的 1 的个数
    #[inline]
    pub fn total_ones(&self) -> i32 {
        self.tree[1].0
    }
}

impl Solution {
    /// 处理三种操作：
    /// 1. 反转 nums1 区间 [l, r] 的 0/1
    /// 2. 令 nums2[i] += nums1[i] * p
    /// 3. 返回 nums2 所有元素和
    pub fn handle_query(nums1: Vec<i32>, nums2: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i64> {
        let mut seg_tree = SegTree::new(&nums1);
        let mut sum_nums2 = nums2.iter().map(|&x| x as i64).sum::<i64>();
        let mut ans = Vec::new();

        for query in queries {
            match query[0] {
                1 => {
                    let l = query[1] as usize;
                    let r = query[2] as usize;
                    seg_tree.reverse(l, r);
                }
                2 => {
                    let p = query[1] as i64;
                    // 只有 nums1 中为 1 的位置会增加，所以增加总量 = ones_count * p
                    sum_nums2 += seg_tree.total_ones() as i64 * p;
                }
                3 => ans.push(sum_nums2),
                _ => unreachable!(),
            }
        }

        ans
    }
}
```
