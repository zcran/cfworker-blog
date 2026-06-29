---
title: "leetcode-线段树17"
date: 2026-06-19T09:37:25+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 距离至少为 K 的交替子序列的最大和

给你一个长度为 n 的整数数组 nums 和一个整数 k。

选择一个下标满足 0 <= i1 < i2 < ... < im < n 的 子序列，并满足：

· 对于每个 1 <= t < m，都有 it+1 - it >= k。
· 所选的值构成一个 严格交替 序列。换句话说，满足以下两种形式之一：
    · nums[i1] < nums[i2] > nums[i3] < ...，或
    · nums[i1] > nums[i2] < nums[i3] > ...

长度为 1 的 子序列 也被认为符合 严格交替 。一个 有效 子序列的得分为其所选元素值的 总和。

返回一个整数，表示有效子序列可能取得的 最大得分。

子序列 是指通过删除原数组中的某些元素或不删除任何元素，并且不改变剩余元素相对顺序后得到的数组。




```
use std::collections::BTreeSet;

/// 树状数组，维护前缀最大值
struct Fenwick {
    tree: Vec<i64>,
}

impl Fenwick {
    fn new(n: usize) -> Self {
        Self { tree: vec![i64::MIN; n + 1] }
    }

    fn update(&mut self, mut i: usize, val: i64) {
        while i < self.tree.len() {
            if val > self.tree[i] {
                self.tree[i] = val;
            }
            i += i & i.wrapping_neg();
        }
    }

    fn query(&self, mut i: usize) -> i64 {
        let mut res = i64::MIN;
        while i > 0 {
            if self.tree[i] > res {
                res = self.tree[i];
            }
            i &= i - 1;
        }
        res
    }
}

impl Solution {
    pub fn max_alternating_sum(nums: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        let n = nums.len();

        // 离散化
        let mut sorted = nums.clone();
        sorted.sort_unstable();
        sorted.dedup();
        let m = sorted.len();

        // 排名 1-based
        let ranks: Vec<usize> = nums
            .iter()
            .map(|x| sorted.binary_search(x).unwrap() + 1)
            .collect();

        let mut f_inc = vec![0i64; n];
        let mut f_dec = vec![0i64; n];

        // 树状数组存储 f_inc 和 f_dec 的最大值
        // bit_inc: 按排名存储 f_inc，用于查询大于某个值的 f_inc
        // bit_dec: 按翻转排名存储 f_dec，用于查询小于某个值的 f_dec
        let mut bit_inc = Fenwick::new(m + 1);
        let mut bit_dec = Fenwick::new(m + 1);

        let mut ans = 0i64;

        for i in 0..n {
            // 将距离 >= k 的元素加入树状数组
            if i >= k {
                let prev = i - k;
                let r = ranks[prev];
                let val_inc = f_inc[prev];
                let val_dec = f_dec[prev];

                // bit_inc 用于查询"大于当前值"，所以要翻转排名：m - r + 1
                // 这样排名越大（值越大），翻转后越小，查询前缀就能得到大于当前值的最大值
                let rev_r = m - r + 1;
                if val_inc > 0 {
                    bit_inc.update(rev_r, val_inc);
                }
                if val_dec > 0 {
                    bit_dec.update(r, val_dec);
                }
            }

            let r = ranks[i];
            let val = nums[i] as i64;

            // 计算 f_inc[i]：最后一段上升，前一个值 < nums[i]
            // 查询 bit_dec 中排名 < r 的最大值
            let best_dec = bit_dec.query(r - 1);
            f_inc[i] = if best_dec > 0 { best_dec + val } else { val };

            // 计算 f_dec[i]：最后一段下降，前一个值 > nums[i]
            // 查询 bit_inc 中翻转排名 < m-r+1 的最大值（即原排名 > r）
            let rev_r = m - r + 1;
            let best_inc = bit_inc.query(rev_r - 1);
            f_dec[i] = if best_inc > 0 { best_inc + val } else { val };

            ans = ans.max(f_inc[i]).max(f_dec[i]);
        }

        ans
    }
}
```
