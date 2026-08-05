---
title: "leetcode-滑动窗口68"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最长奇偶子数组

给你一个下标从 0 开始的整数数组 nums 和一个整数 threshold 。

请你从 nums 的子数组中找出以下标 l 开头、下标 r 结尾 (0 <= l <= r < nums.length) 且满足以下条件的 最长子数组 ：

nums[l] % 2 == 0
对于范围 [l, r - 1] 内的所有下标 i ，nums[i] % 2 != nums[i + 1] % 2
对于范围 [l, r] 内的所有下标 i ，nums[i] <= threshold

以整数形式返回满足题目要求的最长子数组的长度。

注意：子数组 是数组中的一个连续非空元素序列。


```
impl Solution {
    pub fn longest_alternating_subarray(nums: Vec<i32>, threshold: i32) -> i32 {
        let n = nums.len();
        let mut ans = 0;
        let mut i = 0;

        while i < n {
            // 跳过不满足起始条件的元素：奇数或超过阈值
            if nums[i] > threshold || nums[i] % 2 == 1 {
                i += 1;
                continue;
            }

            let start = i;
            i += 1;

            // 扩展窗口：元素不超过阈值且奇偶性交替
            while i < n && nums[i] <= threshold && nums[i] % 2 != nums[i - 1] % 2 {
                i += 1;
            }

            ans = ans.max(i - start);
        }

        ans as i32
    }
}
```
