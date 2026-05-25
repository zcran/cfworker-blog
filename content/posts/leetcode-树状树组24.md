---
title: "leetcode-树状树组24"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 平衡子序列的最大和

给你一个下标从 0 开始的整数数组 nums 。

nums 一个长度为 k 的 子序列 指的是选出 k 个 下标 i0 < i1 < ... < ik-1 ，如果这个子序列满足以下条件，我们说它是 平衡的 ：

对于范围 [1, k - 1] 内的所有 j ，nums[ij] - nums[ij-1] >= ij - ij-1 都成立。
nums 长度为 1 的 子序列 是平衡的。

请你返回一个整数，表示 nums 平衡 子序列里面的 最大元素和 。

一个数组的 子序列 指的是从原数组中删除一些元素（也可能一个元素也不删除）后，剩余元素保持相对顺序得到的 非空 新数组。

```
impl Solution {
    /// 计算最大平衡子序列和。
    /// 平衡子序列定义为：对于子序列中任意相邻元素，满足 nums[j] - j >= nums[i] - i。
    /// 等价于变换 b[i] = nums[i] - i，在 b 的非递减子序列中最大化原 nums[i] 之和。
    ///
    /// 算法：
    /// 1. 计算 b[i] 并离散化（排序后分配排名）。
    /// 2. 按原始顺序遍历，使用树状数组维护前缀（排名 ≤ r）的最大子序列和。
    /// 3. 对于位置 i，查询排名 r = rank[i] 的前缀最大值 prev，
    ///    则 best = max(prev + nums[i], nums[i])（因为可以单独成序列）。
    /// 4. 更新树状数组位置 r 的值为 max(原值, best)。
    /// 5. 最终答案为所有位置的最大值（可能为负）。
    pub fn max_balanced_subsequence_sum(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        // 1. 计算 b[i] = nums[i] - i 并离散化
        let mut vals: Vec<(i32, usize)> = nums
            .iter()
            .enumerate()
            .map(|(i, &x)| (x - i as i32, i))
            .collect();
        vals.sort_by_key(|&(v, _)| v);
        let mut rank = vec![0; n];
        for (r, &(_, idx)) in vals.iter().enumerate() {
            rank[idx] = r;
        }

        // 树状数组维护前缀最大值
        struct BitMax {
            tree: Vec<i64>,
        }
        impl BitMax {
            fn new(size: usize) -> Self {
                Self {
                    tree: vec![i64::MIN; size + 1], // 1‑based
                }
            }
            // 将位置 i（0‑based）的值更新为 max(当前, val)
            fn update(&mut self, i: usize, val: i64) {
                let mut idx = i + 1;
                while idx < self.tree.len() {
                    if val > self.tree[idx] {
                        self.tree[idx] = val;
                    }
                    idx += idx & idx.wrapping_neg();
                }
            }
            // 查询前缀 [0..i] 的最大值
            fn query(&self, i: usize) -> i64 {
                let mut idx = i + 1;
                let mut res = i64::MIN;
                while idx > 0 {
                    if self.tree[idx] > res {
                        res = self.tree[idx];
                    }
                    idx &= idx - 1;
                }
                res
            }
        }

        let mut bit = BitMax::new(n);
        for i in 0..n {
            let r = rank[i];
            let prev = bit.query(r);
            let current = nums[i] as i64;
            // 关键修正：不能使用 max(0)，应允许负数子序列。
            // 取 max(prev + current, current) 确保子序列非空且最优。
            let best = if prev == i64::MIN {
                current
            } else {
                (prev + current).max(current)
            };
            bit.update(r, best);
        }
        // 最终答案即为全局最大值（可能为负）
        bit.query(n - 1)
    }
}
```
