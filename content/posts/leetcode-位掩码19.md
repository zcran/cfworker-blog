---
title: "leetcode-位掩码19"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 两个数组最小的异或值之和

给你两个整数数组 nums1 和 nums2 ，它们长度都为 n 。

两个数组的 异或值之和 为 (nums1[0] XOR nums2[0]) + (nums1[1] XOR nums2[1]) + ... + (nums1[n - 1] XOR nums2[n - 1]) （下标从 0 开始）。

比方说，[1,2,3] 和 [3,2,1] 的 异或值之和 等于 (1 XOR 3) + (2 XOR 2) + (3 XOR 1) = 2 + 0 + 2 = 4 。

请你将 nums2 中的元素重新排列，使得 异或值之和 最小 。

请你返回重新排列之后的 异或值之和 。

```
impl Solution {
    pub fn minimum_xor_sum(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let n = nums1.len();
        let full = 1 << n;
        let mut dp = vec![i32::MAX; full];
        dp[0] = 0;

        // 遍历所有掩码
        for mask in 1..full {
            let cnt = mask.count_ones() as usize; // 已配对的数量 = 已使用的 nums1 前 cnt 个
            // 遍历 mask 中的每一位，尝试将这一位作为最后一个配对
            let mut m = mask;
            while m != 0 {
                let i = m.trailing_zeros() as usize;
                let prev = mask ^ (1 << i);
                // 如果前一个状态可达
                if dp[prev] != i32::MAX {
                    let val = dp[prev] + (nums1[cnt - 1] ^ nums2[i]);
                    dp[mask] = dp[mask].min(val);
                }
                m &= m - 1;
            }
        }
        dp[full - 1]
    }
}
```
