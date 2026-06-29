---
title: "leetcode-有序集合16"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 绝对差值和

给你两个正整数数组 nums1 和 nums2 ，数组的长度都是 n 。

数组 nums1 和 nums2 的 绝对差值和 定义为所有 |nums1[i] - nums2[i]|（0 <= i < n）的 总和（下标从 0 开始）。

你可以选用 nums1 中的 任意一个 元素来替换 nums1 中的 至多 一个元素，以 最小化 绝对差值和。

在替换数组 nums1 中最多一个元素 之后 ，返回最小绝对差值和。因为答案可能很大，所以需要对 10^9 + 7 取余 后返回。

|x| 定义为：

如果 x >= 0 ，值为 x ，或者
如果 x <= 0 ，值为 -x


```
impl Solution {
    /// 计算替换最多一个元素后的最小绝对差值和
    ///
    /// # 思路
    /// 1. 排序 nums1 的副本用于二分查找
    /// 2. 对每个位置，在排序数组中找 nums2[i] 的插入位置
    /// 3. 检查相邻元素能否减少差值，记录最大改进
    /// 4. 总差值减去最大改进并取模
    ///
    /// # 时间复杂度
    /// O(n log n)
    ///
    /// # 空间复杂度
    /// O(n)
    pub fn min_absolute_sum_diff(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums1.len();

        // 排序 nums1 的副本用于二分查找
        let mut sorted = nums1.clone();
        sorted.sort_unstable();

        let mut total_diff: i64 = 0;
        let mut max_improvement: i64 = 0;

        for i in 0..n {
            let (a, b) = (nums1[i], nums2[i]);
            let diff = (a - b).abs() as i64;
            total_diff += diff;

            // 在排序数组中找 b 的插入位置
            let pos = sorted.binary_search(&b).unwrap_or_else(|x| x);

            // 检查前一个元素（如果存在）
            if pos > 0 {
                let new_diff = (b - sorted[pos - 1]).abs() as i64;
                max_improvement = max_improvement.max(diff - new_diff);
            }

            // 检查当前元素（如果存在）
            if pos < n {
                let new_diff = (sorted[pos] - b).abs() as i64;
                max_improvement = max_improvement.max(diff - new_diff);
            }
        }

        ((total_diff - max_improvement) % MOD) as i32
    }
}
```
