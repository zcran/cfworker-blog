---
title: "leetcode-排序61"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 使数组和小于等于 x 的最少时间

给你两个长度相等下标从 0 开始的整数数组 nums1 和 nums2 。每一秒，对于所有下标 0 <= i < nums1.length ，nums1[i] 的值都增加 nums2[i] 。操作 完成后 ，你可以进行如下操作：

选择任一满足 0 <= i < nums1.length 的下标 i ，并使 nums1[i] = 0 。
同时给你一个整数 x 。

请你返回使 nums1 中所有元素之和 小于等于 x 所需要的 最少 时间，如果无法实现，那么返回 -1 。




```
impl Solution {
    pub fn minimum_time(nums1: Vec<i32>, nums2: Vec<i32>, x: i32) -> i32 {
        let n = nums1.len();

        // 按 nums2 升序排序，便于后续 DP 按“增量”从小到大处理
        let mut pairs: Vec<(i32, i32)> = nums1.into_iter().zip(nums2).collect();
        pairs.sort_unstable_by_key(|&(_, b)| b);

        // f[j] 表示在前 i 个元素中选 j 个元素归零后，
        // 这些被选中元素原本“贡献”的 (nums1[i] + j * nums2[i]) 的最大值
        let mut f = vec![0; n + 1];

        for (i, &(a, b)) in pairs.iter().enumerate() {
            // 逆序更新，保证每个元素只使用一次
            for j in (1..=i + 1).rev() {
                f[j] = f[j].max(f[j - 1] + a + b * j as i32);
            }
        }

        let sum1: i32 = pairs.iter().map(|&(a, _)| a).sum();
        let sum2: i32 = pairs.iter().map(|&(_, b)| b).sum();

        // 枚举操作次数 t，判断是否能使总和 <= x
        for t in 0..=n {
            if sum1 + sum2 * t as i32 - f[t] <= x {
                return t as i32;
            }
        }

        -1
    }
}
```
