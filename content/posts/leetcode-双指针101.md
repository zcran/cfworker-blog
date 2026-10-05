---
title: "leetcode-双指针101"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 最大化数组的伟大值

给你一个下标从 0 开始的整数数组 nums 。你需要将 nums 重新排列成一个新的数组 perm 。

定义 nums 的 伟大值 为满足 0 <= i < nums.length 且 perm[i] > nums[i] 的下标数目。

请你返回重新排列 nums 后的 最大 伟大值。


```
impl Solution {
    /// 求重新排列 nums 后的最大伟大值。
    ///
    /// 贪心：排序后，用双指针 i 和 j。
    /// i 指向被超越的较小元素，j 向后寻找更大的元素作为 perm。
    /// 若 nums[j] > nums[i]，则匹配成功，count 加一，i 后移。
    /// 时间复杂度 O(n log n)，空间复杂度 O(1)。
    pub fn maximize_greatness(mut nums: Vec<i32>) -> i32 {
        nums.sort_unstable();

        let mut count = 0;
        let mut i = 0;

        for j in 0..nums.len() {
            if nums[j] > nums[i] {
                count += 1;
                i += 1;
            }
        }

        count
    }
}
```
