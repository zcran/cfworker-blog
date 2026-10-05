---
title: "leetcode-二分查找90"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 统计遥远子数组的数目

给你一个整数数组 nums ，以及两个整数 goal 和 k 。

如果一个 子数组 nums[i..j] 满足其元素和与 goal 之间的 绝对差至少 为 k ，则称其为 遥远的 。

返回 遥远的 子数组的数量。

子数组 是数组中连续的非空元素序列。


```
struct FenwickTree<T> {
    tree: Vec<T>,
}

impl<T: Copy + Default + std::ops::AddAssign + std::ops::Sub<Output = T>> FenwickTree<T> {
    fn new(n: usize) -> Self {
        Self { tree: vec![T::default(); n + 1] }
    }

    // a[i] 增加 val
    fn update(&mut self, mut i: usize, val: T) {
        while i < self.tree.len() {
            self.tree[i] += val;
            i += i & i.wrapping_neg();
        }
    }

    // 前缀和 a[1] + ... + a[i]
    fn pre(&self, mut i: usize) -> T {
        let mut res = T::default();
        while i > 0 {
            res += self.tree[i];
            i &= i - 1;
        }
        res
    }

    // 区间和 a[l] + ... + a[r]
    fn query(&self, l: usize, r: usize) -> T {
        if l > r {
            return T::default();
        }
        self.pre(r) - self.pre(l - 1)
    }
}

impl Solution {
    pub fn distant_subarrays(nums: Vec<i32>, goal: i32, k: i32) -> i64 {
        let n = nums.len();
        let goal = goal as i64;
        let k = k as i64;

        // 前缀和
        let mut sum = vec![0_i64; n + 1];
        for i in 0..n {
            sum[i + 1] = sum[i] + nums[i] as i64;
        }

        // 离散化
        let mut sorted = sum.clone();
        sorted.sort_unstable();
        sorted.dedup();
        let m = sorted.len();

        // 所有子数组总数
        let mut ans = n as i64 * (n as i64 + 1) / 2;

        // 对每个前缀和 s，减去满足 |s - prev - goal| < k 的前缀和 prev 数量
        // 即 prev 落在 (s - goal - k, s - goal + k) 开区间内
        let mut bit: FenwickTree<i64> = FenwickTree::new(m);
        for &s in &sum {
            // 左边界：prev > s - goal - k，即 prev >= s - goal - k + 1
            let l = sorted.partition_point(|&x| x <= s - goal - k) + 1;
            // 右边界：prev < s - goal + k
            let r = sorted.partition_point(|&x| x < s - goal + k);
            ans -= bit.query(l, r);
            // 将当前前缀和加入树状数组
            let pos = sorted.partition_point(|&x| x < s) + 1;
            bit.update(pos, 1);
        }

        ans
    }
}
```
