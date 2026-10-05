---
title: "leetcode-位运算75"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 没有公共位的整数最大乘积

给你一个整数数组 nums。

请你找到两个 不同 的下标 i 和 j，使得 nums[i] * nums[j] 的 乘积最大化 ，并且 nums[i] 和 nums[j] 的二进制表示中没有任何公共的置位 (set bit)。

返回这样一对数的 最大 可能乘积。如果不存在这样的数对，则返回 0。


```
impl Solution {
    pub fn max_product(nums: Vec<i32>) -> i64 {
        // 思路：SOS DP 求"子集最大值"。
        // dp[mask] = nums 中所有为 mask 子集的数的最大值。
        // 标准 SOS DP 按位转移，复杂度 O(V log V)，V = max(nums)。
        // 对每个 val，与其无公共位的数必须属于补集 (full_mask ^ val) 的子集，
        // 其中 full_mask 只需覆盖 val 的所有二进制位。
        // 取该补集子集中的最大值与 val 相乘即可。

        let max_val = *nums.iter().max().unwrap() as usize;
        let mut dp = vec![0i32; max_val + 1];

        // 初始化：出现的值对应自身
        for &val in &nums {
            dp[val as usize] = val;
        }

        // SOS DP：对每个二进制位 b，把所有"第 b 位为 1 的 mask"从"去掉该位"转移
        // 这样 dp[mask] 最终就是 mask 所有子集中的最大值
        let bits = (usize::BITS - max_val.leading_zeros()) as usize;
        for b in 0..bits {
            let bit = 1usize << b;
            for mask in 0..=max_val {
                if mask & bit != 0 {
                    dp[mask] = dp[mask].max(dp[mask ^ bit]);
                }
            }
        }

        let mut ans = 0i64;
        for &val in &nums {
            // 覆盖 val 所有二进制位的掩码
            let width = val.ilog2() + 1;
            let full_mask = (1i32 << width) - 1;
            // 与 val 无公共位的候选必须在 full_mask ^ val 内
            let partner = dp[(full_mask ^ val) as usize];
            ans = ans.max(val as i64 * partner as i64);
        }
        ans
    }
}
```
