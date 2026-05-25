---
title: "leetcode-位掩码22"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 好子集的数目

给你一个整数数组 nums 。如果 nums 的一个子集中，所有元素的乘积可以表示为一个或多个 互不相同的质数 的乘积，那么我们称它为 好子集 。

· 比方说，如果 nums = [1, 2, 3, 4] ：
    · [2, 3] ，[1, 2, 3] 和 [1, 3] 是 好 子集，乘积分别为 6 = 2*3 ，6 = 2*3 和 3 = 3 。
    · [1, 4] 和 [4] 不是 好 子集，因为乘积分别为 4 = 2*2 和 4 = 2*2 。

请你返回 nums 中不同的 好 子集的数目对 109 + 7 取余 的结果。

nums 中的 子集 是通过删除 nums 中一些（可能一个都不删除，也可能全部都删除）元素后剩余元素组成的数组。如果两个子集删除的下标不同，那么它们被视为不同的子集。

```const MOD: i64 = 1_000_000_007;

/// 质因子掩码表（索引 0..30）
/// 用二进制位表示质数集合：bit0=2, bit1=3, bit2=5, bit3=7, bit4=11, bit5=13, bit6=17, bit7=19, bit8=23, bit9=29。
/// -1 表示该数字含有平方因子，不能用于好子集（1 单独处理）。
const MASK: [i32; 31] = [
    -1, 0, 1, 2, -1, 4, 3, 8, -1, -1, 5, 16, -1, 32, 9, 6,
    -1, 64, -1, 128, -1, 10, 17, 256, -1, -1, 33, -1, -1, 512, 7,
];

impl Solution {
    pub fn number_of_good_subsets(nums: Vec<i32>) -> i32 {
        // 1. 统计每个数字（1..30）的出现次数
        let mut cnt = [0; 31];
        for &x in &nums {
            cnt[x as usize] += 1;
        }

        // 2. dp[mask] = 质因子集合为 mask 的非空好子集数量（不含数字1）
        let mut dp = [0i64; 1024];
        dp[0] = 1; // 空集作为基础

        // 3. 处理所有可能的好数字（不含1）
        for num in 2..=30 {
            let mask = MASK[num];
            if mask < 0 {
                continue; // 含有平方因子，跳过
            }
            let c = cnt[num] as i64;
            if c == 0 {
                continue;
            }
            // 倒序遍历避免重复使用本轮更新
            for mj in (0..1024).rev() {
                let mj_i32 = mj as i32;
                if (mask & mj_i32) == 0 {
                    let new_mask = (mask | mj_i32) as usize;
                    dp[new_mask] = (dp[new_mask] + c * dp[mj]) % MOD;
                }
            }
        }

        // 4. 累加所有非空好子集（mask > 0）
        let mut ret = (1..1024).fold(0, |acc, i| (acc + dp[i]) % MOD);

        // 5. 处理数字 1：每个 1 可选或不选，共 2^cnt[1] 倍
        for _ in 0..cnt[1] {
            ret = (ret * 2) % MOD;
        }

        ret as i32
    }
}
```
