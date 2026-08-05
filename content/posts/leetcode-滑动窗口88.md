---
title: "leetcode-滑动窗口88"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 或值至少为 K 的最短子数组 II

给你一个 非负 整数数组 nums 和一个整数 k 。

如果一个数组中所有元素的按位或运算 OR 的值 至少 为 k ，那么我们称这个数组是 特别的 。

请你返回 nums 中 最短特别非空 子数组的长度，如果特别子数组不存在，那么返回 -1 。


```
impl Solution {
    pub fn minimum_subarray_length(nums: Vec<i32>, k: i32) -> i32 {
        const BITS: usize = 31; // i32 最多 31 位（符号位除外）
        let n = nums.len();
        let mut bit_count = [0; BITS];
        let mut ans = i32::MAX;
        let mut left = 0;

        // 计算当前窗口的 OR 值
        let calc_or = |bits: &[i32]| -> i32 {
            let mut val = 0;
            for i in 0..BITS {
                if bits[i] > 0 {
                    val |= 1 << i;
                }
            }
            val
        };

        // 滑动窗口：右指针扩展
        for right in 0..n {
            // 1. 将 nums[right] 的每一位加入窗口
            for i in 0..BITS {
                bit_count[i] += (nums[right] >> i) & 1;
            }

            // 2. 窗口满足条件时，尝试收缩左指针
            while left <= right && calc_or(&bit_count) >= k {
                // 更新答案
                ans = ans.min((right - left + 1) as i32);

                // 移除 nums[left] 的每一位
                for i in 0..BITS {
                    bit_count[i] -= (nums[left] >> i) & 1;
                }
                left += 1;
            }
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```
