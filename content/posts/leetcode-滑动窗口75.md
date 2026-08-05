---
title: "leetcode-滑动窗口75"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 和带限制的子多重集合的数目

给你一个下标从 0 开始的非负整数数组 nums 和两个整数 l 和 r 。

请你返回 nums 中子多重集合的和在闭区间 [l, r] 之间的 子多重集合的数目 。

由于答案可能很大，请你将答案对 109 + 7 取余后返回。

子多重集合 指的是从数组中选出一些元素构成的 无序 集合，每个元素 x 出现的次数可以是 0, 1, ..., occ[x] 次，其中 occ[x] 是元素 x 在数组中的出现次数。

注意：

如果两个子多重集合中的元素排序后一模一样，那么它们两个是相同的 子多重集合 。

空 集合的和是 0 。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_sub_multisets(nums: Vec<i32>, l: i32, r: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 统计每个数字的出现次数
        let mut count = HashMap::new();
        let mut total = 0;
        for &x in &nums {
            *count.entry(x).or_insert(0) += 1;
            total += x;
        }

        let r = r.min(total) as usize;
        let l = l as usize;
        if l > r {
            return 0;
        }

        // f[j] = 组成和为 j 的子多重集合的方案数
        let mut f = vec![0i64; r + 1];

        // 处理数字 0：可以选择任意个 0，共 count[0] + 1 种方案
        let zero_count = count.remove(&0).unwrap_or(0);
        f[0] = (zero_count + 1) as i64;

        let mut sum = 0; // 当前能组成的最大和

        // 处理每个非零数字 x，使用分组背包（多重背包优化）
        for (&x, &c) in count.iter() {
            let x = x as usize;
            let c = c as usize;

            // 前缀和优化：new_f[j] 继承 f[j]（不选 x）
            let mut new_f = f.clone();
            sum = (sum + x * c).min(r);

            // 完全背包部分：每个物品可以选 0..c 个
            // 使用差分优化，避免 O(r*c) 复杂度
            for j in x..=sum {
                // 选至少一个 x：new_f[j] += new_f[j - x]
                new_f[j] = (new_f[j] + new_f[j - x]) % MOD;

                // 减去超过 c 个的情况：new_f[j] -= f[j - (c+1)*x]
                if j >= (c + 1) * x {
                    new_f[j] = (new_f[j] - f[j - (c + 1) * x] + MOD) % MOD;
                }
            }

            f = new_f;
        }

        // 累加 [l, r] 范围内的方案数
        let mut ans = 0;
        for i in l..=r {
            ans = (ans + f[i]) % MOD;
        }

        ans as i32
    }
}
```
