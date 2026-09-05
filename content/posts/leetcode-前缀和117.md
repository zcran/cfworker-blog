---
title: "leetcode-前缀和117"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


##  划分数组得到最小 XOR

给你一个整数数组 nums 和一个整数 k。

你的任务是将 nums 分成 k 个非空的 子数组 。对每个子数组，计算其所有元素的按位 XOR 值。

返回这 k 个子数组中 最大 XOR 的 最小值 。

子数组 是数组中连续的 非空 元素序列。


```
impl Solution {
    pub fn min_xor(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;

        // dp[i][j] = 将前 j 个元素分成 i 段的最小化最大异或值
        // 使用一维数组滚动优化内存
        let mut dp_prev = vec![i32::MAX; n + 1];
        let mut dp_curr = vec![i32::MAX; n + 1];
        dp_prev[0] = 0;

        for i in 1..=k {
            dp_curr.fill(i32::MAX);
            // j 至少为 i，且要给剩余 k-i 段留空间
            for j in i..=n - (k - i) {
                let mut xor_sum = 0;
                // 枚举最后一段的起始位置 l（0-based 索引）
                // 最后一段为 nums[l..j-1]
                for l in (i - 1..j).rev() {
                    xor_sum ^= nums[l];
                    if dp_prev[l] != i32::MAX {
                        let val = dp_prev[l].max(xor_sum);
                        if val < dp_curr[j] {
                            dp_curr[j] = val;
                        }
                    }
                }
            }
            std::mem::swap(&mut dp_prev, &mut dp_curr);
        }

        dp_prev[n]
    }
}
```
