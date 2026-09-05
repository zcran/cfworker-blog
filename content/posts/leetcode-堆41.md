---
title: "leetcode-堆41"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 删除元素后和的最小差值

给你一个下标从 0 开始的整数数组 nums ，它包含 3 * n 个元素。

你可以从 nums 中删除 恰好 n 个元素，剩下的 2 * n 个元素将会被分成两个 相同大小 的部分。

前面 n 个元素属于第一部分，它们的和记为 sumˇfirst 。
后面 n 个元素属于第二部分，它们的和记为 sumˇsecond 。

两部分和的 差值 记为 sumˇfirst - sumˇsecond 。

比方说，sumˇfirst = 3 且 sumˇsecond = 2 ，它们的差值为 1 。
再比方，sumˇfirst = 2 且 sumˇsecond = 3 ，它们的差值为 -1 。

请你返回删除 n 个元素之后，剩下两部分和的 差值的最小值 是多少。


```
use std::collections::BinaryHeap;
use std::cmp::Reverse;

impl Solution {
    pub fn minimum_difference(nums: Vec<i32>) -> i64 {
        let n = nums.len() / 3;

        // left_min[k]: minimum sum of picking n elements from nums[0..n+k]
        let mut left_min = vec![0i64; n + 1];
        let mut left_sum = 0i64;
        let mut max_heap = BinaryHeap::new(); // keeps the n smallest elements seen so far

        for i in 0..n {
            left_sum += nums[i] as i64;
            max_heap.push(nums[i]);
        }
        left_min[0] = left_sum;

        for i in n..2 * n {
            left_sum += nums[i] as i64;
            max_heap.push(nums[i]);
            left_sum -= max_heap.pop().unwrap() as i64;
            left_min[i - n + 1] = left_sum;
        }

        // right_max[k]: maximum sum of picking n elements from nums[n+k..3n]
        let mut right_sum = 0i64;
        let mut min_heap: BinaryHeap<Reverse<i32>> = BinaryHeap::new();

        for i in (2 * n..3 * n).rev() {
            right_sum += nums[i] as i64;
            min_heap.push(Reverse(nums[i]));
        }

        let mut ans = left_min[n] - right_sum;

        for i in (n..2 * n).rev() {
            right_sum += nums[i] as i64;
            min_heap.push(Reverse(nums[i]));
            if let Some(Reverse(val)) = min_heap.pop() {
                right_sum -= val as i64;
            }
            ans = ans.min(left_min[i - n] - right_sum);
        }

        ans
    }
}
```
