---
title: "leetcode-有序集合34"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 分割数组后不同质数的最大数目

给你一个长度为 n 的整数数组 nums，以及一个二维整数数组 queries，其中 queries[i] = [idx, val]。

对于每个查询：

1. 更新 nums[idx] = val。
2. 选择一个满足 1 <= k < n 的整数 k ，将数组分为非空前缀 nums[0..k-1] 和后缀 nums[k..n-1]，使得每部分中 不同 质数的数量之和 最大 。

注意：每次查询对数组的更改将持续到后续的查询中。

返回一个数组，包含每个查询的结果，按给定的顺序排列。

质数是大于 1 的自然数，只有 1 和它本身两个因数。


```
use std::collections::{HashMap, BTreeSet};

const MX: usize = 100_000;

/// 质数筛：np[i] = true 表示 i 不是质数
static mut NP: [bool; MX + 1] = [false; MX + 1];

/// 初始化质数筛（只执行一次）
fn init_sieve() {
    unsafe {
        if NP[0] {
            return; // 已初始化
        }
        NP[0] = true;
        NP[1] = true;
        for i in 2..=MX {
            if !NP[i] {
                let mut j = i;
                while j <= MX / i {
                    NP[i * j] = true;
                    j += 1;
                }
            }
        }
    }
}

/// 判断 x 是否为质数
fn is_prime(x: i32) -> bool {
    if x <= 1 {
        return false;
    }
    unsafe { !NP[x as usize] }
}

/// 懒标记线段树（区间加，维护最大值）
struct LazySegTree {
    n: usize,
    maxv: Vec<i32>, // 区间最大值
    lazy: Vec<i32>, // 懒标记
}

impl LazySegTree {
    /// 新建大小为 n 的线段树，初始值为 0
    fn new(n: usize) -> Self {
        Self {
            n,
            maxv: vec![0; n * 4],
            lazy: vec![0; n * 4],
        }
    }

    /// 区间加 [ql, qr] 统一加上 delta
    fn add(&mut self, ql: usize, qr: usize, delta: i32) {
        if ql > qr {
            return;
        }
        self.add_rec(1, 0, self.n - 1, ql, qr, delta);
    }

    fn add_rec(&mut self, node: usize, l: usize, r: usize, ql: usize, qr: usize, delta: i32) {
        if ql <= l && r <= qr {
            self.maxv[node] += delta;
            self.lazy[node] += delta;
            return;
        }
        self.push(node);
        let mid = (l + r) >> 1;
        if ql <= mid {
            self.add_rec(node << 1, l, mid, ql, qr, delta);
        }
        if qr > mid {
            self.add_rec(node << 1 | 1, mid + 1, r, ql, qr, delta);
        }
        self.maxv[node] = self.maxv[node << 1].max(self.maxv[node << 1 | 1]);
    }

    /// 查询整个区间 [0, n-1] 的最大值
    fn query_all(&self) -> i32 {
        self.maxv[1]
    }

    /// 下传懒标记
    fn push(&mut self, node: usize) {
        let delta = self.lazy[node];
        if delta != 0 {
            let left = node << 1;
            let right = left | 1;
            self.maxv[left] += delta;
            self.lazy[left] += delta;
            self.maxv[right] += delta;
            self.lazy[right] += delta;
            self.lazy[node] = 0;
        }
    }
}

impl Solution {
    pub fn maximum_count(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        init_sieve();

        let n = nums.len();
        let mut nums = nums;

        // pos[prime] = 该质数在数组中出现的所有索引（有序）
        let mut pos: HashMap<i32, BTreeSet<usize>> = HashMap::new();

        // 初始化 pos
        for (i, &x) in nums.iter().enumerate() {
            if is_prime(x) {
                pos.entry(x).or_insert_with(BTreeSet::new).insert(i);
            }
        }

        // 线段树维护：对于每个质数，若出现次数 >= 2，则在其 [首次出现, 最后出现] 区间 +1
        let mut seg = LazySegTree::new(n);
        for set in pos.values() {
            if set.len() >= 2 {
                let first = *set.iter().next().unwrap();
                let last = *set.iter().next_back().unwrap();
                seg.add(first, last, 1);
            }
        }

        let mut ans = Vec::with_capacity(queries.len());

        for q in &queries {
            let idx = q[0] as usize;
            let new_val = q[1];
            let old_val = nums[idx];

            // 更新数组值
            nums[idx] = new_val;

            // --- 1. 移除旧值 ---
            if is_prime(old_val) {
                let set = pos.get_mut(&old_val).unwrap();
                // 如果这个质数之前出现 >= 2 次，撤销其区间贡献
                if set.len() >= 2 {
                    let first = *set.iter().next().unwrap();
                    let last = *set.iter().next_back().unwrap();
                    seg.add(first, last, -1);
                }
                // 移除当前索引
                set.remove(&idx);
                // 如果移除后仍有 >= 2 个，重新贡献
                if set.len() >= 2 {
                    let first = *set.iter().next().unwrap();
                    let last = *set.iter().next_back().unwrap();
                    seg.add(first, last, 1);
                }
                // 如果集合为空，删除该键
                if set.is_empty() {
                    pos.remove(&old_val);
                }
            }

            // --- 2. 添加新值 ---
            if is_prime(new_val) {
                let set = pos.entry(new_val).or_insert_with(BTreeSet::new);
                // 如果插入前已有 >= 2 个，撤销旧区间
                if set.len() >= 2 {
                    let first = *set.iter().next().unwrap();
                    let last = *set.iter().next_back().unwrap();
                    seg.add(first, last, -1);
                }
                // 插入新索引
                set.insert(idx);
                // 如果插入后 >= 2 个，贡献新区间
                if set.len() >= 2 {
                    let first = *set.iter().next().unwrap();
                    let last = *set.iter().next_back().unwrap();
                    seg.add(first, last, 1);
                }
            }

            // --- 3. 计算结果 ---
            // pos.len() = 整个数组中不同质数的数量
            // seg.query_all() = 切一刀能获得的最大额外收益（即同时出现在左右两边的质数数量）
            let result = pos.len() as i32 + seg.query_all();
            ans.push(result);
        }

        ans
    }
}
```
