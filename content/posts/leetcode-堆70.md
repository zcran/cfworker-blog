---
title: "leetcode-堆70"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 至多 K 次交换后最大子数组和

给你一个整数数组 nums 和一个整数 k。

你可以对数组执行 至多 k 次交换操作。

在一次交换操作中，你可以选择任意两个下标 i 和 j 并交换 nums[i] 和 nums[j]。

返回一个整数，表示在执行交换后 可能的最大 子数组 和。


```
impl Solution {
    /// 核心思想：枚举所有子数组，用两个树状数组维护「子数组内」和「子数组外」的元素分布。
    ///
    /// 最优策略：将子数组内最小的 need_swap 个数，与子数组外最大的 need_swap 个数交换。
    /// need_swap 动态维护（可增可减），上限为 min(k, 子数组长度, 子数组外长度)。
    ///
    /// 树状数组支持：
    /// - 第 k 小元素查询（kth）
    /// - 前 k 小元素和查询（pre_sum）
    pub fn max_sum(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;

        // ---- 离散化 ----
        let mut sorted = nums.clone();
        sorted.sort_unstable();
        sorted.dedup();
        let m = sorted.len();

        let rank = |x: i32| -> usize {
            sorted.binary_search(&x).unwrap() + 1 // 树状数组下标从 1 开始
        };

        // ---- 树状数组 ----
        struct Fenwick {
            cnt: Vec<i32>,
            sum: Vec<i64>,
            high_bit: usize,
            values: Vec<i32>, // 离散化后的值，索引 i 对应 values[i-1]
        }

        impl Fenwick {
            fn new(values: Vec<i32>) -> Self {
                let n = values.len();
                let hb = 1usize << (usize::BITS - 1 - n.leading_zeros());
                Self {
                    cnt: vec![0; n + 1],
                    sum: vec![0; n + 1],
                    high_bit: hb,
                    values,
                }
            }

            fn add(&mut self, mut i: usize, delta: i32) {
                let val = self.values[i - 1] as i64;
                while i < self.cnt.len() {
                    self.cnt[i] += delta;
                    self.sum[i] += val * delta as i64;
                    i += i & i.wrapping_neg();
                }
            }

            // 第 k 小的元素值（k >= 1）
            fn kth(&self, mut k: i32) -> i32 {
                let mut i = 0usize;
                let mut bit = self.high_bit;
                while bit > 0 {
                    let nxt = i | bit;
                    if nxt < self.cnt.len() && self.cnt[nxt] < k {
                        k -= self.cnt[nxt];
                        i = nxt;
                    }
                    bit >>= 1;
                }
                self.values[i]
            }

            // 前 k 小的元素之和（k >= 1）
            fn prefix_sum(&self, mut k: i32) -> i64 {
                let mut s = 0i64;
                let mut i = 0usize;
                let mut bit = self.high_bit;
                while bit > 0 {
                    let nxt = i | bit;
                    if nxt < self.cnt.len() && self.cnt[nxt] < k {
                        k -= self.cnt[nxt];
                        s += self.sum[nxt];
                        i = nxt;
                    }
                    bit >>= 1;
                }
                s + self.values[i] as i64 * k as i64
            }
        }

        // 预构建全量树状数组
        let mut all_tree = Fenwick::new(sorted.clone());
        let mut total = 0i64;
        let ranks: Vec<usize> = nums
            .iter()
            .map(|&x| {
                total += x as i64;
                let r = rank(x);
                all_tree.add(r, 1);
                r
            })
            .collect();

        let mut ans = i64::MIN;

        // ---- 枚举子数组左端点 ----
        for left in 0..n {
            let mut in_tree = Fenwick::new(sorted.clone());
            let mut out_tree = Fenwick {
                cnt: all_tree.cnt.clone(),
                sum: all_tree.sum.clone(),
                high_bit: all_tree.high_bit,
                values: sorted.clone(),
            };

            let mut need_swap = 0usize;
            let mut sub_sum = 0i64;

            // ---- 枚举子数组右端点 ----
            for right in left..n {
                let rk = ranks[right];
                let x = nums[right] as i64;

                sub_sum += x;
                in_tree.add(rk, 1);
                out_tree.add(rk, -1);

                let sz = right - left + 1;
                let out_sz = n - sz;

                // 尝试增加一次交换
                if need_swap < k && need_swap < sz && need_swap < out_sz {
                    if in_tree.kth(need_swap as i32 + 1) < out_tree.kth(out_sz as i32 - need_swap as i32) {
                        need_swap += 1;
                    }
                }

                // 尝试减少一次交换
                if need_swap > 0 {
                    if in_tree.kth(need_swap as i32) >= out_tree.kth(out_sz as i32 - need_swap as i32 + 1) {
                        need_swap -= 1;
                    }
                }

                // 计算交换带来的增量
                let delta = if need_swap > 0 {
                    let in_sum = in_tree.prefix_sum(need_swap as i32);
                    let out_small_sum = out_tree.prefix_sum(out_sz as i32 - need_swap as i32);
                    let out_sum = total - sub_sum - out_small_sum;
                    out_sum - in_sum
                } else {
                    0
                };

                ans = ans.max(sub_sum + delta);
            }
        }

        ans
    }
}
```
