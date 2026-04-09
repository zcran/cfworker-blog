---
title: "leetcode-单调队列20"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 开销小于等于 K 的子数组数目

给你一个整数数组 nums，和一个整数 k。

对于任意子数组 nums[l..r]，其 开销 定义为：

cost = (max(nums[l..r]) - min(nums[l..r])) * (r - l + 1)。

返回一个整数，表示 nums 中开销 小于或等于 k 的子数组数量。

子数组 是数组中连续的 非空 元素序列。

```
use std::collections::VecDeque;

impl Solution {
    pub fn count_subarrays(nums: Vec<i32>, k: i64) -> i64 {
        let n = nums.len();
        let mut ans = 0i64;
        let mut left = 0;

        // min_deque 存储下标，对应的元素值单调递增（队首是最小值）
        let mut min_deque: VecDeque<usize> = VecDeque::new();
        // max_deque 存储下标，对应的元素值单调递减（队首是最大值）
        let mut max_deque: VecDeque<usize> = VecDeque::new();

        for right in 0..n {
            let val = nums[right];

            // 更新单调队列
            while !min_deque.is_empty() && nums[*min_deque.back().unwrap()] >= val {
                min_deque.pop_back();
            }
            min_deque.push_back(right);

            while !max_deque.is_empty() && nums[*max_deque.back().unwrap()] <= val {
                max_deque.pop_back();
            }
            max_deque.push_back(right);

            // 检查当前窗口是否满足 cost <= k
            // cost = (max - min) * (right - left + 1)
            while left <= right {
                let max_val = nums[*max_deque.front().unwrap()] as i64;
                let min_val = nums[*min_deque.front().unwrap()] as i64;
                let cost = (max_val - min_val) * (right - left + 1) as i64;

                if cost > k {
                    left += 1;
                    // 如果左指针移出了队列首部记录的下标，弹出该下标
                    if !min_deque.is_empty() && *min_deque.front().unwrap() < left {
                        min_deque.pop_front();
                    }
                    if !max_deque.is_empty() && *max_deque.front().unwrap() < left {
                        max_deque.pop_front();
                    }
                } else {
                    break;
                }
            }

            // 以 right 为结尾的满足条件的子数组个数为窗口长度
            ans += (right - left + 1) as i64;
        }

        ans
    }
}
```
