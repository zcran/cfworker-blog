---
title: "leetcode-单调队列5"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 绝对差不超过限制的最长连续子数组

给你一个整数数组 nums ，和一个表示限制的整数 limit，请你返回最长连续子数组的长度，该子数组中的任意两个元素之间的绝对差必须小于或者等于 limit。

```

use std::collections::VecDeque;

impl Solution {
    pub fn longest_subarray(nums: Vec<i32>, limit: i32) -> i32 {
        let mut l = 0;
        let mut min_q = VecDeque::new();
        let mut max_q = VecDeque::new();
        for num in &nums {
            while max_q.back().map_or(false, |x| x < num) {
                max_q.pop_back();
            }
            while min_q.back().map_or(false, |x| x > num) {
                min_q.pop_back();
            }
            max_q.push_back(*num);
            min_q.push_back(*num);
            if max_q[0] - min_q[0] > limit {
                if nums[l] == max_q[0] {
                    max_q.pop_front();
                }
                if nums[l] == min_q[0] {
                    min_q.pop_front();
                }
                l += 1
            }
        }
        (nums.len() - l) as i32
    }
}
```
