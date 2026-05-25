---
title: "leetcode-树状树组23"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 子数组不同元素数目的平方和 II

给你一个下标从 0 开始的整数数组 nums 。

定义 nums 一个子数组的 不同计数 值如下：

令 nums[i..j] 表示 nums 中所有下标在 i 到 j 范围内的元素构成的子数组（满足 0 <= i <= j < nums.length ），那么我们称子数组 nums[i..j] 中不同值的数目为 nums[i..j] 的不同计数。
请你返回 nums 中所有子数组的 不同计数 的 平方 和。

由于答案可能会很大，请你将它对 109 + 7 取余 后返回。

子数组指的是一个数组里面一段连续 非空 的元素序列。

```
const MOD: i32 = 1_000_000_007;

/// 模加法（取模后保证结果在 [0, MOD) 内）
#[inline]
fn mod_add(a: i32, b: i32) -> i32 {
    let s = a + b;
    if s >= MOD { s - MOD } else { s }
}

/// 线段树维护两个信息：
/// - sum: 区间内所有数的和 (Σ a_i)  mod MOD
/// - sum_sq: 区间内所有数的平方和 (Σ a_i^2) mod MOD
/// 支持区间加法，并可以在 O(log n) 内更新和查询。
struct SegTree {
    sum: Vec<i32>,      // 区间元素和
    sum_sq: Vec<i32>,   // 区间元素平方和
    lazy: Vec<i32>,     // 懒标记（待加值）
}

impl SegTree {
    /// 建树，大小为 n（下标 0..n-1 有效，内部数组长度 4n）
    fn new(n: usize) -> Self {
        let size = n << 2; // 4 * n
        Self {
            sum: vec![0; size],
            sum_sq: vec![0; size],
            lazy: vec![0; size],
        }
    }

    #[inline(always)]
    fn lson(p: usize) -> usize {
        p << 1
    }

    #[inline(always)]
    fn rson(p: usize) -> usize {
        p << 1 | 1
    }

    /// 下推懒标记到左右孩子
    fn push_down(&mut self, p: usize, l: usize, r: usize) {
        let add = self.lazy[p];
        if add == 0 {
            return;
        }
        let mid = (l + r) >> 1;
        let ls = Self::lson(p);
        let rs = Self::rson(p);
        let len_left = mid - l + 1;
        let len_right = r - mid;

        // 辅助函数：更新某个结点的信息
        let apply = |sum: &mut i32, sum_sq: &mut i32, lazy: &mut i32, add: i32, len: usize| {
            let add_len = (len as i64 * add as i64 % MOD as i64) as i32;
            let add_sq_len = (add as i64 * add as i64 % MOD as i64 * len as i64 % MOD as i64) as i32;
            let cross_term = (2 * add as i64 * (*sum) as i64 % MOD as i64) as i32;
            *sum_sq = mod_add(*sum_sq, mod_add(cross_term, add_sq_len));
            *sum = mod_add(*sum, add_len);
            *lazy = mod_add(*lazy, add);
        };

        apply(
            &mut self.sum[ls],
            &mut self.sum_sq[ls],
            &mut self.lazy[ls],
            add,
            len_left,
        );
        apply(
            &mut self.sum[rs],
            &mut self.sum_sq[rs],
            &mut self.lazy[rs],
            add,
            len_right,
        );
        self.lazy[p] = 0;
    }

    /// 合并左右孩子信息
    fn push_up(&mut self, p: usize) {
        let ls = Self::lson(p);
        let rs = Self::rson(p);
        self.sum[p] = mod_add(self.sum[ls], self.sum[rs]);
        self.sum_sq[p] = mod_add(self.sum_sq[ls], self.sum_sq[rs]);
    }

    /// 区间加：将 [al, ar] 内每个数增加 val（模 MOD）
    pub fn add(&mut self, al: usize, ar: usize, val: i32, l: usize, r: usize, p: usize) {
        if al <= l && r <= ar {
            let len = r - l + 1;
            let add_len = (len as i64 * val as i64 % MOD as i64) as i32;
            let add_sq_len = (val as i64 * val as i64 % MOD as i64 * len as i64 % MOD as i64) as i32;
            let cross_term = (2 * val as i64 * self.sum[p] as i64 % MOD as i64) as i32;
            self.sum_sq[p] = mod_add(self.sum_sq[p], mod_add(cross_term, add_sq_len));
            self.sum[p] = mod_add(self.sum[p], add_len);
            self.lazy[p] = mod_add(self.lazy[p], val);
            return;
        }
        self.push_down(p, l, r);
        let mid = (l + r) >> 1;
        if al <= mid {
            self.add(al, ar, val, l, mid, Self::lson(p));
        }
        if ar > mid {
            self.add(al, ar, val, mid + 1, r, Self::rson(p));
        }
        self.push_up(p);
    }

    /// 区间查询平方和
    pub fn query(&mut self, ql: usize, qr: usize, l: usize, r: usize, p: usize) -> i32 {
        if ql <= l && r <= qr {
            return self.sum_sq[p];
        }
        self.push_down(p, l, r);
        let mid = (l + r) >> 1;
        let mut res = 0;
        if ql <= mid {
            res = mod_add(res, self.query(ql, qr, l, mid, Self::lson(p)));
        }
        if qr > mid {
            res = mod_add(res, self.query(ql, qr, mid + 1, r, Self::rson(p)));
        }
        res
    }
}

impl Solution {
    /// 计算所有子数组的「不同元素和的平方」之和。
    /// 算法：从左到右扫描，维护每个值最近一次出现的位置。
    /// 对于当前元素 x，它会对所有以当前位置 i 结尾的子数组的贡献产生变化：
    /// 将 x 的最近出现位置的前一个区间减去 1，并将 [0, i] 区间加上 1，
    /// 然后查询前缀平方和，累加到答案。
    pub fn sum_counts(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let max_val = *nums.iter().max().unwrap() as usize;
        // last_pos[val] 存储值 val 最近一次出现的下标（0‑based），初始为 None
        let mut last_pos = vec![None; max_val + 1];

        // 线段树需要支持下标 0..=n（包含 n 作为边界）
        let mut seg = SegTree::new(n + 1);
        let mut ans = 0;

        for (i, &val) in nums.iter().enumerate() {
            let v = val as usize;
            // 如果该值之前出现过，清除上次出现的影响（区间 -1）
            if let Some(prev) = last_pos[v] {
                seg.add(0, prev, -1, 0, n, 1);
            }
            // 更新最近出现位置为当前下标
            last_pos[v] = Some(i);
            // 当前元素对所有包含它的子数组贡献 +1（即区间 [0, i] 加 1）
            seg.add(0, i, 1, 0, n, 1);
            // 查询前缀平方和，累加到答案
            ans = mod_add(ans, seg.query(0, i, 0, n, 1));
        }
        ans
    }
}
```
