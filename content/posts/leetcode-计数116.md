---
title: "leetcode-计数116"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计特殊三元组

给你一个整数数组 nums。

特殊三元组 定义为满足以下条件的下标三元组 (i, j, k)：

0 <= i < j < k < n，其中 n = nums.length
nums[i] == nums[j] * 2
nums[k] == nums[j] * 2

返回数组中 特殊三元组 的总数。

由于答案可能非常大，请返回结果对 10^9 + 7 取余数后的值。


```
use std::collections::HashMap;

impl Solution {
    /// 统计满足 nums[i] == nums[j] * 2 == nums[k] 的三元组 (i, j, k) 数量，
    /// 其中 i < j < k。答案对 1_000_000_007 取模。
    pub fn special_triplets(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let mut left = HashMap::new();
        let mut right = HashMap::new();

        // 初始：所有元素视为在 j 的右侧
        for &v in &nums {
            *right.entry(v).or_insert(0i64) += 1;
        }

        let mut ans = 0i64;
        for &v in &nums {
            // 当前元素作为中间点 j，从右侧移出
            *right.get_mut(&v).unwrap() -= 1;

            let target = v * 2;
            let l = *left.get(&target).unwrap_or(&0);
            let r = *right.get(&target).unwrap_or(&0);
            ans = (ans + l * r) % MOD;

            // 当前元素归入左侧
            *left.entry(v).or_insert(0i64) += 1;
        }

        ans as i32
    }
}
```
