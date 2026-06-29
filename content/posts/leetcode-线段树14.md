---
title: "leetcode-线段树14"
date: 2026-06-19T09:37:25+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 固定长度子数组中的最小逆序对数目

给你一个长度为 n 的整数数组 nums 和一个整数 k。

逆序对 是指 nums 中满足 i < j 且 nums[i] > nums[j] 的一对下标 (i, j)。

子数组 的 逆序对数量 是指该子数组内逆序对的个数。

返回 nums 中所有长度为 k 的 子数组 中的 最小 逆序对数量。

子数组 是数组中一个连续的非空元素序列。


```
use std::collections::HashMap;

/// 树状数组（Fenwick Tree），支持单点更新和前缀和查询
struct Fenwick {
    tree: Vec<i64>,
}

impl Fenwick {
    /// 创建大小为 n 的树状数组
    fn new(n: usize) -> Self {
        Fenwick { tree: vec![0; n + 1] }
    }

    /// 在位置 i 处增加 delta
    #[inline]
    fn add(&mut self, mut i: usize, delta: i64) {
        let n = self.tree.len();
        while i < n {
            self.tree[i] += delta;
            i += i & (!i + 1); // i & -i 的低位运算
        }
    }

    /// 查询前缀 [1..i] 的和
    #[inline]
    fn sum(&self, mut i: usize) -> i64 {
        let mut s = 0;
        while i > 0 {
            s += self.tree[i];
            i &= i - 1; // 移除最低位的 1
        }
        s
    }
}

impl Solution {
    /// 返回所有长度为 k 的子数组中的最小逆序对数量
    ///
    /// 思路：滑动窗口 + 树状数组
    /// 1. 对数组元素进行离散化
    /// 2. 用树状数组维护当前窗口内元素的频次
    /// 3. 每次滑动窗口时 O(log n) 更新逆序对数量
    ///
    /// 时间复杂度 O(n log n)，空间复杂度 O(n)
    pub fn min_inversion_count(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;

        // 长度为 1 的子数组没有逆序对
        if k <= 1 {
            return 0;
        }

        // ---------- 离散化 ----------
        let mut sorted = nums.clone();
        sorted.sort_unstable();
        sorted.dedup();

        let mut rank = HashMap::with_capacity(sorted.len());
        for (i, &val) in sorted.iter().enumerate() {
            rank.insert(val, i + 1); // 1-based 索引
        }

        // 将原数组转换为排名数组
        let comp: Vec<usize> = nums.iter().map(|x| rank[x]).collect();
        let m = sorted.len();

        // ---------- 初始化第一个窗口 ----------
        let mut fw = Fenwick::new(m);
        let mut inv = 0i64;

        // 计算第一个长度为 k 的子数组的逆序对数量
        for i in 0..k {
            let x = comp[i];
            // 当前元素左边大于它的元素个数 = i - 小于等于它的元素个数
            let le = fw.sum(x); // 小于等于 x 的元素个数
            inv += i as i64 - le;
            fw.add(x, 1);
        }

        let mut ans = inv;

        // ---------- 滑动窗口 ----------
        for i in k..n {
            // 移除离开窗口的元素 comp[i - k]
            let x_del = comp[i - k];
            // 被移除元素左侧小于它的元素个数（这些逆序对会消失）
            let less = fw.sum(x_del - 1);
            inv -= less;
            fw.add(x_del, -1);

            // 添加进入窗口的元素 comp[i]
            let x_add = comp[i];
            // 新元素右侧大于它的元素个数 = (窗口大小 - 1) - 小于等于它的元素个数
            let le = fw.sum(x_add);
            inv += (k as i64 - 1) - le;
            fw.add(x_add, 1);

            // 更新答案
            if inv < ans {
                ans = inv;
            }
        }

        ans
    }
}
```
