---
title: "leetcode-单调队列17"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 统计极差最大为 K 的分割方式数

给你一个整数数组 nums 和一个整数 k。你的任务是将 nums 分割成一个或多个 非空 的连续子段，使得每个子段的 最大值 与 最小值 之间的差值 不超过 k。

返回在此条件下将 nums 分割的总方法数。

由于答案可能非常大，返回结果需要对 109 + 7 取余数。


```
use std::collections::VecDeque;

impl Solution {
    pub fn count_partitions(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let mod_val = 1_000_000_007i64;

        // dp[i] = 以nums[i-1]结尾的有效分割数
        let mut dp = vec![0i64; n + 1];
        let mut prefix_sum = vec![0i64; n + 1];

        // 单调队列维护窗口内的最小值和最大值
        let mut min_deque: VecDeque<usize> = VecDeque::new();
        let mut max_deque: VecDeque<usize> = VecDeque::new();

        dp[0] = 1;
        prefix_sum[0] = 1;
        let mut left = 0;

        for right in 0..n {
            // 维护单调递增队列（存最小值索引）
            while let Some(&last) = min_deque.back() {
                if nums[last] >= nums[right] {
                    min_deque.pop_back();
                } else {
                    break;
                }
            }
            min_deque.push_back(right);

            // 维护单调递减队列（存最大值索引）
            while let Some(&last) = max_deque.back() {
                if nums[last] <= nums[right] {
                    max_deque.pop_back();
                } else {
                    break;
                }
            }
            max_deque.push_back(right);

            // 收缩窗口直到满足条件
            while left <= right &&
                  nums[*max_deque.front().unwrap()] - nums[*min_deque.front().unwrap()] > k {
                if let Some(&front) = min_deque.front() {
                    if front == left {
                        min_deque.pop_front();
                    }
                }
                if let Some(&front) = max_deque.front() {
                    if front == left {
                        max_deque.pop_front();
                    }
                }
                left += 1;
            }

            // 计算dp[right+1]
            if left == 0 {
                dp[right + 1] = prefix_sum[right];
            } else {
                dp[right + 1] = (prefix_sum[right] - prefix_sum[left - 1] + mod_val) % mod_val;
            }

            prefix_sum[right + 1] = (prefix_sum[right] + dp[right + 1]) % mod_val;
        }

        dp[n] as i32
    }
}
```
