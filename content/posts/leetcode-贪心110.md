---
title: "leetcode-贪心110"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使数组中所有元素相等的最小操作数 II

给你两个整数数组 nums1 和 nums2 ，两个数组长度都是 n ，再给你一个整数 k 。你可以对数组 nums1 进行以下操作：

选择两个下标 i 和 j ，将 nums1[i] 增加 k ，将 nums1[j] 减少 k 。换言之，nums1[i] = nums1[i] + k 且 nums1[j] = nums1[j] - k 。
如果对于所有满足 0 <= i < n 都有 num1[i] == nums2[i] ，那么我们称 nums1 等于 nums2 。

请你返回使 nums1 等于 nums2 的 最少 操作数。如果没办法让它们相等，请你返回 -1 。


```
impl Solution {
    pub fn min_operations(nums1: Vec<i32>, nums2: Vec<i32>, k: i32) -> i64 {
        // k == 0 时无法做任何调整，只能判断两数组是否已相等
        if k == 0 {
            return if nums1 == nums2 { 0 } else { -1 };
        }

        let k = k as i64;
        let mut balance = 0i64;  // 净差值（正表示 nums1 偏大需转出，负表示需转入）
        let mut total = 0i64;    // 所有 |差值| / k 之和

        for (&a, &b) in nums1.iter().zip(nums2.iter()) {
            let diff = a as i64 - b as i64;
            if diff % k != 0 {
                return -1; // 差值不是 k 的整数倍，无法对齐
            }
            let d = diff / k;
            balance += d;
            total += d.abs();
        }

        // 每次操作转移一个 k，转出与转入必须平衡，故总操作数为 total / 2
        if balance == 0 { total / 2 } else { -1 }
    }
}
```
