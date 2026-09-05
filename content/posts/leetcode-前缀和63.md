---
title: "leetcode-前缀和63"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最小平均差

给你一个下标从 0 开始长度为 n 的整数数组 nums 。

下标 i 处的 平均差 指的是 nums 中 前 i + 1 个元素平均值和 后 n - i - 1 个元素平均值的 绝对差 。两个平均值都需要 向下取整 到最近的整数。

请你返回产生 最小平均差 的下标。如果有多个下标最小平均差相等，请你返回 最小 的一个下标。

注意：

两个数的 绝对差 是两者差的绝对值。
 n 个元素的平均值是 n 个元素之 和 除以（整数除法） n 。
0 个元素的平均值视为 0 。


```
impl Solution {
    pub fn minimum_average_difference(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 计算总和
        let total_sum: i64 = nums.iter().map(|&x| x as i64).sum();

        let mut prefix_sum: i64 = 0;
        let mut min_diff = i64::MAX;
        let mut ans = 0;

        for i in 0..n {
            // 前 i+1 个元素的和
            prefix_sum += nums[i] as i64;

            // 前部分的平均值（向下取整）
            let left_avg = prefix_sum / (i + 1) as i64;

            // 后部分的平均值（向下取整），如果没有剩余元素则为 0
            let right_avg = if i == n - 1 {
                0
            } else {
                (total_sum - prefix_sum) / (n - i - 1) as i64
            };

            // 计算平均差的绝对值
            let diff = (left_avg - right_avg).abs();

            // 更新最小平均差对应的下标（使用 > 确保返回最小下标）
            if diff < min_diff {
                min_diff = diff;
                ans = i;
            }
        }

        ans as i32
    }
}
```
