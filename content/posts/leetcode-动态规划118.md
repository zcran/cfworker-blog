---
title: "leetcode-动态规划118"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 构造最长非递减子数组

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，长度均为 n 。

让我们定义另一个下标从 0 开始、长度为 n 的整数数组，nums3 。对于范围 [0, n - 1] 的每个下标 i ，你可以将 nums1[i] 或 nums2[i] 的值赋给 nums3[i] 。

你的任务是使用最优策略为 nums3 赋值，以最大化 nums3 中 最长非递减子数组 的长度。

以整数形式表示并返回 nums3 中 最长非递减 子数组的长度。

注意：子数组 是数组中的一个连续非空元素序列。


```
impl Solution {
    pub fn max_non_decreasing_length(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let n = nums1.len();
        // dp0: 以当前下标选择 nums1 结尾的最长非递减子数组长度
        // dp1: 以当前下标选择 nums2 结尾的最长非递减子数组长度
        let mut dp0 = 1;
        let mut dp1 = 1;
        let mut ans = 1;

        for i in 1..n {
            // 先计算上一轮的候选值，避免相互影响
            let a = nums1[i - 1];
            let b = nums2[i - 1];
            let c = nums1[i];
            let d = nums2[i];

            // 当前选 nums1[i]，可接在上一轮选 nums1[i-1] 或 nums2[i-1] 之后
            let n0 = if c >= a { dp0 + 1 } else { 1 }
                .max(if c >= b { dp1 + 1 } else { 1 });
            // 当前选 nums2[i]，同理
            let n1 = if d >= a { dp0 + 1 } else { 1 }
                .max(if d >= b { dp1 + 1 } else { 1 });

            dp0 = n0;
            dp1 = n1;
            ans = ans.max(dp0).max(dp1);
        }

        ans
    }
}
```
