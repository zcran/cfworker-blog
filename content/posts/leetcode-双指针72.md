---
title: "leetcode-双指针72"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## K 和数对的最大数目

给你一个整数数组 nums 和一个整数 k 。

每一步操作中，你需要从数组中选出和为 k 的两个整数，并将它们移出数组。

返回你可以对数组执行的最大操作数。


```
impl Solution {
    pub fn max_operations(nums: Vec<i32>, k: i32) -> i32 {
        use std::collections::HashMap;

        let mut count = HashMap::with_capacity(nums.len());
        let mut operations = 0;

        for num in nums {
            let complement = k - num;

            // 如果存在配对数字，立即配对
            if let Some(c) = count.get_mut(&complement) {
                if *c > 0 {
                    *c -= 1;
                    operations += 1;
                    continue;
                }
            }

            // 否则记录当前数字出现次数
            *count.entry(num).or_insert(0) += 1;
        }

        operations
    }
}
```
