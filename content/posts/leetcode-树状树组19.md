---
title: "leetcode-树状树组19"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---



## 满足不等式的数对数目

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，两个数组的大小都为 n ，同时给你一个整数 diff ，统计满足以下条件的 数对 (i, j) ：

·0 <= i < j <= n - 1 且
·nums1[i] - nums1[j] <= nums2[i] - nums2[j] + diff.

请你返回满足条件的 数对数目 。

```
use std::cmp::Ordering;

/// 树状数组（Fenwick Tree），支持单点加和前缀查询（1‑based）
struct BIT {
    tree: Vec<i32>,
}

impl BIT {
    /// 创建大小为 `size` 的树状数组（内部数组长度为 size+1，忽略索引0）
    fn new(size: usize) -> Self {
        BIT {
            tree: vec![0; size + 1],
        }
    }

    /// 在索引 `x`（1‑based）处增加 1
    fn add(&mut self, mut x: usize) {
        while x < self.tree.len() {
            self.tree[x] += 1;
            x += x & (!x + 1); // lowbit
        }
    }

    /// 查询前缀和 [1..x]
    fn query(&self, mut x: usize) -> i32 {
        let mut res = 0;
        while x > 0 {
            res += self.tree[x];
            x -= x & (!x + 1);
        }
        res
    }
}

impl Solution {
    /// 统计满足条件的数对 (i, j) 的数量：
    /// i < j 且 a[i] - nums2[i] <= a[j] - nums2[j] + diff
    ///
    /// 算法：
    /// 1. 计算数组 c[i] = a[i] - nums2[i]
    /// 2. 将 c 离散化，得到从 1 开始的顺序编号
    /// 3. 遍历 c（顺序保证 i < j），对于当前值 x：
    ///    - 在离散化数组中二分查找 ≤ x+diff 的最大位置，查询树状数组该前缀内的元素个数
    ///    - 将该元素加入树状数组（对应离散化位置 +1）
    /// 4. 累加查询结果即为答案
    pub fn number_of_pairs(a: Vec<i32>, nums2: Vec<i32>, diff: i32) -> i64 {
        let n = a.len();
        // 1. 计算 c[i] = a[i] - nums2[i]
        let mut c: Vec<i32> = (0..n).map(|i| a[i] - nums2[i]).collect();

        // 2. 离散化：收集所有可能出现的值 (c 中的值 以及 c[i] + diff)
        let mut all_vals: Vec<i32> = c.iter().copied().collect();
        for &x in &c {
            all_vals.push(x + diff);
        }
        all_vals.sort_unstable();
        all_vals.dedup();

        // 辅助函数：将原值转换为离散化后的索引（1‑based）
        let compress = |val: i32| -> usize {
            all_vals.binary_search(&val).unwrap() + 1
        };

        let m = all_vals.len();
        let mut bit = BIT::new(m);

        let mut ans = 0i64;
        for &x in &c {
            // 查询有多少个已处理的 y 满足 y <= x + diff
            let pos = compress(x + diff);
            ans += bit.query(pos) as i64;
            // 将当前 x 加入树状数组
            let pos_x = compress(x);
            bit.add(pos_x);
        }
        ans
    }
}
```
