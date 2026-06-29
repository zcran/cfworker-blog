---
title: "leetcode-线段树16"
date: 2026-06-19T09:37:25+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 好子序列查询

给你一个长度为 n 的整数数组 nums 和一个整数 p。

如果 nums 的一个 非空子序列 满足以下条件，则称其为 好子序列：

· 其长度 严格小于 n。
· 其所有元素的 最大公约数（GCD）恰好等于 p。

另给定一个长度为 q 的二维整数数组 queries，其中 queries[i] = [indi, vali] 表示你需要将 nums[indi] 更新为 vali。

在每次查询更新后，判断当前数组中是否存在 任意一个好子序列。

返回一个整数，表示使得数组中存在 好子序列 的查询 次数。

子序列 是指通过删除原序列中的某些元素或不删除任何元素，并且不改变剩余元素相对顺序后得到的序列。

gcd(a, b) 表示 a 和 b 的 最大公约数。


```
use std::collections::HashMap;

/// 线段树，支持单点更新和区间 GCD 查询
struct SegmentTree {
    n: usize,
    target_gcd: i32,
    tree: Vec<i32>,
}

impl SegmentTree {
    /// 用数组 a 和目标 GCD 构建线段树
    fn new(a: &[i32], target_gcd: i32) -> Self {
        let n = a.len();
        let mut tree = vec![0; 4 * n + 4];
        let mut seg = SegmentTree { n, target_gcd, tree };
        seg.build(a, 1, 0, n - 1);
        seg
    }

    /// 构建线段树
    fn build(&mut self, a: &[i32], node: usize, l: usize, r: usize) {
        if l == r {
            // 只有能被 target_gcd 整除的元素才对 GCD 有贡献
            self.tree[node] = if a[l] % self.target_gcd == 0 { a[l] } else { 0 };
            return;
        }
        let mid = (l + r) >> 1;
        let left = node << 1;
        let right = left | 1;
        self.build(a, left, l, mid);
        self.build(a, right, mid + 1, r);
        self.tree[node] = gcd(self.tree[left], self.tree[right]);
    }

    /// 单点更新
    fn update(&mut self, idx: usize, val: i32) {
        self.update_rec(1, 0, self.n - 1, idx, val);
    }

    fn update_rec(&mut self, node: usize, l: usize, r: usize, idx: usize, val: i32) {
        if l == r {
            self.tree[node] = if val % self.target_gcd == 0 { val } else { 0 };
            return;
        }
        let mid = (l + r) >> 1;
        let left = node << 1;
        let right = left | 1;
        if idx <= mid {
            self.update_rec(left, l, mid, idx, val);
        } else {
            self.update_rec(right, mid + 1, r, idx, val);
        }
        self.tree[node] = gcd(self.tree[left], self.tree[right]);
    }

    /// 区间查询 GCD
    fn query(&self, ql: usize, qr: usize) -> i32 {
        if ql > qr {
            return 0;
        }
        self.query_rec(1, 0, self.n - 1, ql, qr)
    }

    fn query_rec(&self, node: usize, l: usize, r: usize, ql: usize, qr: usize) -> i32 {
        if ql <= l && r <= qr {
            return self.tree[node];
        }
        let mid = (l + r) >> 1;
        let left = node << 1;
        let right = left | 1;
        if qr <= mid {
            return self.query_rec(left, l, mid, ql, qr);
        }
        if ql > mid {
            return self.query_rec(right, mid + 1, r, ql, qr);
        }
        let l_res = self.query_rec(left, l, mid, ql, qr);
        let r_res = self.query_rec(right, mid + 1, r, ql, qr);
        gcd(l_res, r_res)
    }

    /// 查询整个数组的 GCD
    fn query_all(&self) -> i32 {
        self.tree[1]
    }

    /// 检查是否存在好子序列
    /// 好子序列条件：长度 < n，且所有元素的 GCD == target_gcd
    ///
    /// 如果整个数组的 GCD != target_gcd，则不存在
    /// 如果数组中有非 target_gcd 的元素，删除所有 target_gcd 的倍数即可
    /// 特殊情况：当所有元素都是 target_gcd 的倍数时，需要检查是否可以通过删除一个元素得到目标 GCD
    fn check(&self, count_p: usize, n: usize) -> bool {
        // 整个数组的 GCD 必须等于 target_gcd
        if self.query_all() != self.target_gcd {
            return false;
        }

        // 如果存在不是 target_gcd 倍数的元素，删除它即可得到目标 GCD
        // 实际上，我们需要检查是否存在一个元素，删除后剩余元素的 GCD == target_gcd
        // 由于整个数组 GCD == target_gcd，如果数组中有一个元素不是 target_gcd 的倍数，
        // 删除它后 GCD 可能变为 target_gcd（因为整个数组 GCD 是 target_gcd）
        // 如果所有元素都是 target_gcd 的倍数且 n > 1，可以删除一个元素，GCD 仍为 target_gcd

        // 如果有元素不是 target_gcd 的倍数，一定存在好子序列
        if count_p < n {
            return true;
        }

        // 所有元素都是 target_gcd 的倍数
        // 需要删除一个元素，且剩余元素的 GCD 仍为 target_gcd
        // 当 n > 1 时，删除一个元素后数组非空且长度 < n
        // 只要删除的元素不是唯一能维持 GCD 为 target_gcd 的元素，就存在好子序列
        // 对于 n 比较小的情况，直接枚举检查
        if n <= 6 {
            // 枚举删除每个元素
            for i in 0..n {
                let left_gcd = if i > 0 { self.query(0, i - 1) } else { 0 };
                let right_gcd = if i + 1 < n { self.query(i + 1, n - 1) } else { 0 };
                if gcd(left_gcd, right_gcd) == self.target_gcd {
                    return true;
                }
            }
            return false;
        }

        // n > 6 且所有元素都是 target_gcd 的倍数
        // 由于整个数组 GCD == target_gcd，删除任何一个元素，剩余元素的 GCD 仍是 target_gcd
        // 因为 target_gcd 整除所有元素，删除一个不影响它们的公因子
        true
    }
}

/// 计算两个数的最大公约数
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a.abs()
}

impl Solution {
    /// 统计使数组存在好子序列的查询次数
    ///
    /// 好子序列：长度 < n，GCD 恰好等于 p
    ///
    /// 核心思路：
    /// 1. 只有能被 p 整除的元素才可能对 GCD 有贡献
    /// 2. 如果整个数组的 GCD != p，则不存在好子序列
    /// 3. 如果数组中存在不是 p 的倍数的元素，删除它即可
    /// 4. 如果所有元素都是 p 的倍数，需要检查删除一个元素后 GCD 是否仍为 p
    ///
    /// 时间复杂度 O((n + q) log n)，空间复杂度 O(n)
    pub fn count_good_subseq(nums: Vec<i32>, p: i32, queries: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();
        let mut nums = nums;

        // 统计初始数组中能被 p 整除的元素个数
        let mut count_p = nums.iter().filter(|&&x| x % p == 0).count();

        // 构建线段树
        let mut seg = SegmentTree::new(&nums, p);

        let mut ans = 0;

        for query in &queries {
            let idx = query[0] as usize;
            let val = query[1];

            // 更新 count_p
            if nums[idx] % p == 0 {
                count_p -= 1;
            }
            if val % p == 0 {
                count_p += 1;
            }

            // 更新数组和线段树
            nums[idx] = val;
            seg.update(idx, val);

            // 检查是否存在好子序列
            if seg.check(count_p, n) {
                ans += 1;
            }
        }

        ans
    }
}
```
