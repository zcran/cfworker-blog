---
title: "leetcode-前缀和88"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计趣味子数组的数目

给你一个下标从 0 开始的整数数组 nums ，以及整数 modulo 和整数 k 。

请你找出并统计数组中 趣味子数组 的数目。

如果 子数组 nums[l..r] 满足下述条件，则称其为 趣味子数组 ：

在范围 [l, r] 内，设 cnt 为满足 nums[i] % modulo == k 的索引 i 的数量。并且 cnt % modulo == k 。

以整数形式表示并返回趣味子数组的数目。

注意：子数组是数组中的一个连续非空的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_interesting_subarrays(nums: Vec<i32>, modulo: i32, k: i32) -> i64 {
        // 前缀和模 modulo 的计数
        let mut prefix_count = HashMap::new();
        prefix_count.insert(0, 1);

        let mut prefix = 0i32;
        let mut ans = 0i64;

        for &num in &nums {
            // 如果当前元素满足条件，前缀和加1
            if num % modulo == k {
                prefix = (prefix + 1) % modulo;
            }

            // 需要找到前缀值 = (prefix - k) mod modulo 的数量
            let target = (prefix - k).rem_euclid(modulo);
            ans += *prefix_count.get(&target).unwrap_or(&0) as i64;

            // 记录当前前缀值
            *prefix_count.entry(prefix).or_insert(0) += 1;
        }

        ans
    }
}
```
