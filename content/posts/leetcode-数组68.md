---
title: "leetcode-数组68"
date: 2026-10-10T20:38:45+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最大有效数对和

给你一个长度为 n 的整数数组 nums 和一个整数 k 。

如果满足以下条件，则下标对 (i, j) 被称为 有效 的：

0 <= i < j < n
j - i >= k

返回所有有效对中的 nums[i] + nums[j] 的 最大 值。


```
impl Solution {
    pub fn max_valid_pair_sum(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let mut ans = 0;
        let mut mx = 0; // 满足 j - i >= k 的 nums[i] 的最大值

        // 对每个 j，维护 nums[j-k] 及之前元素的最大值
        for j in k..nums.len() {
            mx = mx.max(nums[j - k]);
            ans = ans.max(mx + nums[j]);
        }

        ans
    }
}
```
