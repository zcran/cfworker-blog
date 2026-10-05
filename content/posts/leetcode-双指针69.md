---
title: "leetcode-双指针69"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最大得分

你有两个 有序 且数组内元素互不相同的数组 nums1 和 nums2 。

一条 合法路径 定义如下：

· 选择数组 nums1 或者 nums2 开始遍历（从下标 0 处开始）。
· 从左到右遍历当前数组。
· 如果你遇到了 nums1 和 nums2 中都存在的值，那么你可以切换路径到另一个数组对应数字处继续遍历（但在合法路径中重复数字只会被统计一次）。

得分 定义为合法路径中不同数字的和。

请你返回 所有可能 合法路径 中的最大得分。由于答案可能很大，请你将它对 10^9 + 7 取余后返回。


```
impl Solution {
    pub fn max_sum(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let (n1, n2) = (nums1.len(), nums2.len());
        let (mut i, mut j) = (0, 0);
        let (mut sum1, mut sum2) = (0i64, 0i64);
        let mut ans = 0i64;

        // 双指针遍历两个有序数组，遇到公共元素时分段处理
        while i < n1 && j < n2 {
            if nums1[i] < nums2[j] {
                sum1 += nums1[i] as i64;
                i += 1;
            } else if nums1[i] > nums2[j] {
                sum2 += nums2[j] as i64;
                j += 1;
            } else {
                // 公共元素：计入两个数组的当前段和，并选择较大分支
                sum1 += nums1[i] as i64;
                sum2 += nums2[j] as i64;
                ans = (ans + sum1.max(sum2)) % MOD;
                // 重置段累加器，开始下一段
                sum1 = 0;
                sum2 = 0;
                i += 1;
                j += 1;
            }
        }

        // 处理剩余未遍历的尾部元素（此时两个数组中最多只有一个还有剩余）
        while i < n1 {
            sum1 += nums1[i] as i64;
            i += 1;
        }
        while j < n2 {
            sum2 += nums2[j] as i64;
            j += 1;
        }
        ans = (ans + sum1.max(sum2)) % MOD;

        ans as i32
    }
}
```
