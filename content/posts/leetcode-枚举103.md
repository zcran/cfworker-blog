---
title: "leetcode-枚举103"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 求和后首尾数字相同的有效子数组 I

给你一个整数数组 nums 和一个整数数字 x。

如果一个 子数组 nums[l..r] 的元素和同时满足以下两个条件，则认为该子数组是 有效子数组：

该和的首位数字等于 x。

该和的末位数字等于 x。

返回有效子数组的数量。

子数组 是数组中一个连续、非空 的元素序列。


```
impl Solution {
    pub fn count_valid_subarrays(nums: Vec<i32>, x: i32) -> i32 {
        let n = nums.len();
        let mut ans = 0;

        // 枚举所有子数组起点
        for i in 0..n {
            let mut sum: i64 = 0;
            // 从起点 i 扩展子数组
            for &num in &nums[i..] {
                sum += num as i64;

                // 检查末位数字
                if sum % 10 != x as i64 {
                    continue;
                }

                // 检查首位数字
                let mut first = sum;
                while first > 9 {
                    first /= 10;
                }
                if first == x as i64 {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
