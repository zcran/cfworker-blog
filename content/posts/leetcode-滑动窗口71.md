---
title: "leetcode-滑动窗口71"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计完全子数组的数目

给你一个由 正 整数组成的数组 nums 。

如果数组中的某个子数组满足下述条件，则称之为 完全子数组 ：

子数组中 不同 元素的数目等于整个数组不同元素的数目。

返回数组中 完全子数组 的数目。

子数组 是数组中的一个连续非空序列。


```
use std::collections::{HashMap, HashSet};

impl Solution {
    pub fn count_complete_subarrays(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        // 整个数组的不同元素个数
        let total_distinct = nums.iter().collect::<HashSet<_>>().len();

        let mut freq = HashMap::new();
        let mut right = 0;
        let mut ans = 0;

        // 滑动窗口 [left, right)，维护窗口内的不同元素个数
        for left in 0..n {
            // 扩展右边界，直到窗口包含所有不同元素
            while right < n && freq.len() < total_distinct {
                *freq.entry(nums[right]).or_insert(0) += 1;
                right += 1;
            }

            // 如果窗口包含了所有不同元素，则所有以 left 开头、right' >= right 的子数组都满足条件
            if freq.len() == total_distinct {
                ans += (n - right + 1) as i32;
            }

            // 移动左边界，移除 nums[left]
            if let Some(count) = freq.get_mut(&nums[left]) {
                *count -= 1;
                if *count == 0 {
                    freq.remove(&nums[left]);
                }
            }
        }

        ans
    }
}
```
