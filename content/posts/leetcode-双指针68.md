---
title: "leetcode-双指针68"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 满足条件的子序列数目

给你一个整数数组 nums 和一个整数 target 。

请你统计并返回 nums 中能满足其最小元素与最大元素的 和 小于或等于 target 的 非空 子序列的数目。

由于答案可能很大，请将结果对 10^9 + 7 取余后返回。


```
impl Solution {
    pub fn num_subseq(mut nums: Vec<i32>, target: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        nums.sort_unstable();
        let n = nums.len();

        // 预计算 2 的幂次，用于快速计算子序列数量
        let mut pow2 = vec![1; n];
        for i in 1..n {
            pow2[i] = (pow2[i - 1] * 2) % MOD;
        }

        let mut ans = 0;
        let mut right = n - 1;

        // 双指针：对于每个最小元素 nums[left]，找到最大的 right
        // 使得 nums[left] + nums[right] <= target
        for left in 0..n {
            // 如果最小元素的两倍都大于 target，后面的元素更大，直接退出
            if nums[left] * 2 > target {
                break;
            }

            // 右指针左移直到满足条件
            while right > left && nums[left] + nums[right] > target {
                right -= 1;
            }

            // 如果 right < left，说明没有有效的子序列
            if right < left {
                break;
            }

            // 在 left..=right 范围内，包含 left 的子序列数量为 2^(right-left)
            // 因为除了 left 必须选，其他元素可选可不选
            ans = (ans + pow2[right - left]) % MOD;
        }

        ans as i32
    }
}
```
