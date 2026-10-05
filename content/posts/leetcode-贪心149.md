---
title: "leetcode-贪心149"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 执行操作后不同元素的最大数量

给你一个整数数组 nums 和一个整数 k。

你可以对数组中的每个元素 最多 执行 一次 以下操作：

将一个在范围 [-k, k] 内的整数加到该元素上。

返回执行这些操作后，nums 中可能拥有的不同元素的 最大 数量。




```
impl Solution {
    pub fn max_distinct_elements(mut nums: Vec<i32>, k: i32) -> i32 {
        // 贪心：排序后，对每个元素在 [num-k, num+k] 内尽量取"刚好大于上一个选中值"的数，
        // 以给后续元素留出更多空间。若该区间没有可用值（上界 <= 上一个值），则跳过该元素。
        nums.sort_unstable();

        let mut count = 0;
        let mut prev = i32::MIN; // 上一个被选中的值

        for num in nums {
            // 取不小于 prev + 1 的最小可行值，且不超过 num + k
            let candidate = (num - k).max(prev + 1);
            if candidate <= num + k {
                count += 1;
                prev = candidate;
            }
        }
        count
    }
}
```
