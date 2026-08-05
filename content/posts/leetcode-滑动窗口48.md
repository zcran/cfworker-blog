---
title: "leetcode-滑动窗口48"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


##  使数组连续的最少操作数

给你一个整数数组 nums 。每一次操作中，你可以将 nums 中 任意 一个元素替换成 任意 整数。

如果 nums 满足以下条件，那么它是 连续的 ：

nums 中所有元素都是 互不相同 的。
nums 中 最大 元素与 最小 元素的差等于 nums.length - 1 。

比方说，nums = [4, 2, 5, 3] 是 连续的 ，但是 nums = [1, 2, 3, 5, 6] 不是连续的 。

请你返回使 nums 连续 的 最少 操作次数。


```
use std::collections::HashSet;

impl Solution {
    pub fn min_operations(nums: Vec<i32>) -> i32 {
        let n = nums.len() as i32;

        // 去重并排序
        let mut unique: Vec<i32> = nums.into_iter().collect::<HashSet<_>>().into_iter().collect();
        unique.sort_unstable();

        let m = unique.len() as i32;
        let mut max_keep = 0;  // 最多可以保留多少个元素
        let mut right = 0;     // 滑动窗口右边界

        // 滑动窗口：对于每个左边界，找到能放在长度为n的区间内的最多元素
        for left in 0..m {
            // 窗口右边界右移，直到超出区间范围
            while right < m && unique[right as usize] - unique[left as usize] < n {
                right += 1;
            }
            // right - left 即为当前窗口能保留的元素个数
            max_keep = max_keep.max(right - left);
        }

        // 最少操作数 = 总长度 - 最多可保留的元素数
        n - max_keep
    }
}
```
