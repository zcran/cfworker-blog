---
title: "leetcode-动态规划82"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 两个子序列的最大点积

给你两个数组 nums1 和 nums2 。

请你返回 nums1 和 nums2 中两个长度相同的 非空 子序列的最大点积。

数组的非空子序列是通过删除原数组中某些元素（可能一个也不删除）后剩余数字组成的序列，但不能改变数字间相对顺序。比方说，[2,3,5] 是 [1,2,3,4,5] 的一个子序列而 [1,5,3] 不是。


```
impl Solution {
    pub fn max_dot_product(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let n = nums2.len();
        // dp[j] 表示当前处理到的 nums1 前缀与 nums2[0..=j] 能得到的最大点积
        // 用 i32::MIN 表示不可达，避免空序列的 0 干扰负数结果
        let mut dp = vec![i32::MIN; n];

        for &x in &nums1 {
            // prev 保存上一行 dp[j-1] 的值，即 f[i-1][j-1]
            let mut prev = i32::MIN;
            for j in 0..n {
                let old = dp[j];          // 保存 f[i-1][j]
                let xij = x * nums2[j];

                // 至少可以选择 x 与 nums2[j] 组成一对
                let mut best = xij;
                // 继承不使用 x 的情况
                if old != i32::MIN {
                    best = best.max(old);
                }
                // 继承不使用 nums2[j] 的情况（当前行前一个状态）
                if j > 0 {
                    best = best.max(dp[j - 1]);
                }
                // 接在 f[i-1][j-1] 之后
                if prev != i32::MIN {
                    best = best.max(prev + xij);
                }

                dp[j] = best;
                prev = old; // 为下一列更新 prev
            }
        }

        dp[n - 1]
    }
}
```
