---
title: "leetcode-滑动窗口21"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## K 个不同整数的子数组

给定一个正整数数组 nums和一个整数 k，返回 nums 中 「好子数组」 的数目。

如果 nums 的某个子数组中不同整数的个数恰好为 k，则称 nums 的这个连续、不一定不同的子数组为 「好子数组 」。

例如，[1,2,3,1,2] 中有 3 个不同的整数：1，2，以及 3。

子数组 是数组的 连续 部分。


```
use std::collections::HashMap;

impl Solution {
    pub fn subarrays_with_k_distinct(nums: Vec<i32>, k: i32) -> i32 {
        // 辅助函数：计算不同整数个数 <= k 的子数组数量
        fn at_most_k_distinct(nums: &[i32], k: i32) -> i32 {
            if k < 0 {
                return 0;
            }
            let k = k as usize;
            let mut freq = HashMap::new();
            let mut left = 0;
            let mut count = 0;

            for (right, &num) in nums.iter().enumerate() {
                *freq.entry(num).or_insert(0) += 1;

                // 超过 k 种不同整数，收缩左边界
                while freq.len() > k {
                    let left_num = nums[left];
                    *freq.get_mut(&left_num).unwrap() -= 1;
                    if freq[&left_num] == 0 {
                        freq.remove(&left_num);
                    }
                    left += 1;
                }

                // 以 right 结尾的合法子数组数量 = right - left + 1
                count += right - left + 1;
            }

            count as i32
        }

        // 恰好 k 个 = 最多 k 个 - 最多 k-1 个
        at_most_k_distinct(&nums, k) - at_most_k_distinct(&nums, k - 1)
    }
}
```
