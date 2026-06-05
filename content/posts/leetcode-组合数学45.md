---
title: "leetcode-组合数学45"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---



## 有效子序列的数量


给你一个整数数组 nums。

数组的 强度 定义为数组中所有元素的 按位或 (Bitwise OR)  。

如果移除某个 子序列 会使剩余数组的 强度严格减少 ，那么该子序列被称为 有效子序列 。

返回数组中 有效子序列 的数量。由于答案可能很大，请返回结果对 10^9 + 7 取模后的值。

子序列 是一个 非空 数组，它是由另一个数组删除一些（或不删除任何）元素，并且不改变剩余元素的相对顺序得到的。

空数组的按位或为 0。




```
impl Solution {
    /// 返回 nums 的所有子序列（包括空序列）中，按位或（OR）严格小于整个数组按位或的子序列个数。
    /// 结果对 1_000_000_007 取模。
    pub fn count_effective(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();

        // 预处理 2 的幂
        let mut pow2 = vec![1; n + 1];
        for i in 1..=n {
            pow2[i] = (pow2[i - 1] * 2) % MOD;
        }

        // 特殊情况：所有元素相同（此时 or_ = 该元素）
        // 所有非空子序列的 OR 都等于 or_，只有空子序列的 OR 为 0 < or_（除非 or_ = 0）
        // 因此严格小于 or_ 的子序列个数 = 1（空序列）
        if nums.iter().all(|&x| x == nums[0]) {
            return 1;
        }

        // 整个数组的按位或
        let or_ = nums.iter().fold(0, |acc, &x| acc | x) as usize;
        // 位宽（or_ 的最高有效位位置）
        let w = 32 - (or_ as u32).leading_zeros() as usize;

        // f[s] 统计 nums 中恰好等于 s 的个数（s 是 or_ 的子集掩码）
        let mut f = vec![0; 1 << w];
        for &x in &nums {
            f[x as usize] += 1;
        }

        // SOS DP：对于每个位 i（or_ 中为 1 的位），计算所有超集的 f 之和
        // 最终 f[s] 表示 nums 中元素是 s 的子集的个数
        for i in 0..w {
            if (or_ >> i) & 1 == 0 {
                continue;
            }
            let bit = 1 << i;
            for s in 0..(1 << w) {
                if (s & bit) != 0 {
                    f[s] += f[s ^ bit];
                }
            }
        }

        // 容斥：计算按位或严格小于 or_ 的子序列个数
        let mut ans = pow2[n]; // 所有子序列（包括空）个数
        let mut sub = or_;     // 枚举 or_ 的所有子集（包括自身和 0）
        loop {
            let sign = if ((or_ ^ sub).count_ones() % 2) == 0 { 1 } else { -1 };
            // 注意：f[sub] 表示 nums 中元素是 sub 的子集的个数，
            // 这些元素构成的子序列的按位或一定是 sub 的子集
            ans = (ans - sign * pow2[f[sub] as usize]) % MOD;
            if sub == 0 {
                break;
            }
            sub = (sub - 1) & or_;
        }
        // 确保结果非负
        ((ans % MOD + MOD) % MOD) as i32
    }
}

```
