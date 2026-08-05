---
title: "leetcode-滑动窗口39"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 将 x 减到 0 的最小操作数

给你一个整数数组 nums 和一个整数 x 。每一次操作时，你应当移除数组 nums 最左边或最右边的元素，然后从 x 中减去该元素的值。请注意，需要 修改 数组以供接下来的操作使用。

如果可以将 x 恰好 减到 0 ，返回 最小操作数 ；否则，返回 -1 。


```
impl Solution {
    pub fn min_operations(nums: Vec<i32>, x: i32) -> i32 {
        let total: i32 = nums.iter().sum();
        let target = total - x; // 需要找的中间连续子数组的和

        // 如果总和不满足要求
        if target < 0 {
            return -1;
        }
        if target == 0 {
            return nums.len() as i32;
        }

        let n = nums.len();
        let mut left = 0;
        let mut window_sum = 0;
        let mut max_mid_len = 0;

        // 滑动窗口：找和为 target 的最长连续子数组
        for right in 0..n {
            window_sum += nums[right];

            // 收缩窗口直到 window_sum <= target
            while window_sum > target && left <= right {
                window_sum -= nums[left];
                left += 1;
            }

            // 如果找到目标和，更新最大长度
            if window_sum == target {
                max_mid_len = max_mid_len.max(right - left + 1);
            }
        }

        // 如果找不到任何满足条件的中间子数组，返回 -1
        if max_mid_len == 0 {
            return -1;
        }

        // 最小操作数 = 总长度 - 中间子数组的最大长度
        n as i32 - max_mid_len as i32
    }
}
```
