---
title: "leetcode-滑动窗口22"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 最大连续1的个数 III

给定一个二进制数组 nums 和一个整数 k，假设最多可以翻转 k 个 0 ，则返回执行操作后 数组中连续 1 的最大个数 。


```
impl Solution {
    pub fn longest_ones(nums: Vec<i32>, k: i32) -> i32 {
        let mut left = 0;
        let mut zero_count = 0;
        let mut max_len = 0;

        for (right, &num) in nums.iter().enumerate() {
            // 统计窗口内 0 的个数
            if num == 0 {
                zero_count += 1;
            }

            // 如果 0 的个数超过 k，收缩左边界
            while zero_count > k {
                if nums[left] == 0 {
                    zero_count -= 1;
                }
                left += 1;
            }

            // 更新最大窗口长度
            max_len = max_len.max(right - left + 1);
        }

        max_len as i32
    }
}
```
