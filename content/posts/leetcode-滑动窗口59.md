---
title: "leetcode-滑动窗口59"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最长优雅子数组

给你一个由 正 整数组成的数组 nums 。

如果 nums 的子数组中位于 不同 位置的每对元素按位 与（AND）运算的结果等于 0 ，则称该子数组为 优雅 子数组。

返回 最长 的优雅子数组的长度。

子数组 是数组中的一个 连续 部分。

注意：长度为 1 的子数组始终视作优雅子数组。



```
impl Solution {
    pub fn longest_nice_subarray(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n <= 1 {
            return n as i32;
        }

        let mut max_len = 1;
        let mut left = 0;
        let mut used_bits = 0;  // 当前窗口内所有数字的按位或

        for right in 0..n {
            // 如果新元素与当前窗口有冲突，左移指针直到冲突消除
            while (used_bits & nums[right]) != 0 {
                used_bits ^= nums[left];  // 从窗口中移除左端元素
                left += 1;
            }

            // 将当前元素加入窗口
            used_bits |= nums[right];

            // 更新最长长度
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
