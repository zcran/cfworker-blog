---
title: "leetcode-树状树组31"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 交易逆序对的总数


在股票交易中，如果前一天的股价高于后一天的股价，则可以认为存在一个「交易逆序对」。请设计一个程序，输入一段时间内的股票交易记录 record，返回其中存在的「交易逆序对」总数。



示例 1：

输入：record = [9, 7, 5, 4, 6]
输出：8
解释：交易中的逆序对为 (9, 7), (9, 5), (9, 4), (9, 6), (7, 5), (7, 4), (7, 6), (5, 4)。


```
use std::collections::BTreeSet;

/// 树状数组（Fenwick Tree），支持单点增加和前缀和查询
struct FenwickTree(Vec<i32>);

impl FenwickTree {
    /// 创建大小为 `n` 的树状数组（内部索引 1..=n）
    fn new(n: usize) -> Self {
        Self(vec![0; n + 1])
    }

    /// 计算 lowbit
    #[inline]
    const fn lowbit(x: usize) -> usize {
        x & x.wrapping_neg()
    }

    /// 在排名 `index`（0‑based）处增加 `delta`
    fn add(&mut self, index: usize, delta: i32) {
        let mut i = index + 1; // 转换为 1‑based
        while i < self.0.len() {
            self.0[i] += delta;
            i += Self::lowbit(i);
        }
    }

    /// 查询前缀和 [0, index]（包含），index 为 0‑based
    fn prefix_sum(&self, index: usize) -> i32 {
        let mut i = index + 1; // 转换为 1‑based
        let mut sum = 0;
        while i > 0 {
            sum += self.0[i];
            i -= Self::lowbit(i);
        }
        sum
    }
}

impl Solution {
    /// 计算逆序对个数：i < j 且 nums[i] > nums[j]
    pub fn reverse_pairs(nums: Vec<i32>) -> i32 {
        // 1. 离散化：将数值映射到 0..m-1 的排名（升序）
        let sorted: Vec<i32> = nums.iter().copied().collect::<BTreeSet<_>>()
            .into_iter().collect();
        let rank_of = |&x: &i32| sorted.binary_search(&x).unwrap();

        let m = sorted.len();
        let mut bit = FenwickTree::new(m);
        let mut ans = 0;

        // 2. 遍历数组，统计每个元素左侧大于它的元素个数
        for x in &nums {
            let r = rank_of(x);
            // 已插入的小于等于当前值的元素个数
            let le = bit.prefix_sum(r);
            // 已插入的总元素个数
            let total = bit.prefix_sum(m - 1);
            // 大于当前值的个数 = 总数 - 小于等于的个数
            let greater = total - le;
            ans += greater;
            // 将当前元素插入树状数组
            bit.add(r, 1);
        }
        ans
    }
}
```
