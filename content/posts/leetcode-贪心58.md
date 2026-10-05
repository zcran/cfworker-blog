---
title: "leetcode-贪心58"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 乘积为正数的最长子数组长度

给你一个整数数组 nums ，请你求出乘积为正数的最长子数组的长度。

一个数组的子数组是由原数组中零个或者更多个连续数字组成的数组。

请你返回乘积为正数的最长子数组长度。


```
impl Solution {
    pub fn get_max_len(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut max_len = 0;
        let mut first_neg: i32 = -1; // 当前段第一个负数的下标，-1 表示尚未遇到
        let mut last_neg: i32 = -1;  // 当前段最后一个负数的下标
        let mut neg_count = 0;       // 当前段负数个数
        let mut start: i32 = 0;      // 当前段起点（0 之后的位置）

        for i in 0..=n {
            // 遇到 0 或到达末尾，结算当前段
            if i == n || nums[i] == 0 {
                let i = i as i32;
                if neg_count % 2 == 0 {
                    // 负数个数为偶，整段乘积为正
                    max_len = max_len.max(i - start);
                } else {
                    // 负数个数为奇，去掉第一个负数及其之前，或去掉最后一个负数及其之后
                    max_len = max_len.max(i - first_neg - 1);
                    max_len = max_len.max(last_neg - start);
                }
                start = i + 1;
                first_neg = -1;
                last_neg = -1;
                neg_count = 0;
                continue;
            }
            if nums[i] < 0 {
                if first_neg == -1 {
                    first_neg = i as i32;
                }
                last_neg = i as i32;
                neg_count += 1;
            }
        }
        max_len
    }
}
```
