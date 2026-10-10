---
title: "leetcode-哈希表60"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 找出不同元素数目差数组

给你一个下标从 0 开始的数组 nums ，数组长度为 n 。

nums 的 不同元素数目差 数组可以用一个长度为 n 的数组 diff 表示，其中 diff[i] 等于前缀 nums[0, ..., i] 中不同元素的数目 减去 后缀 nums[i + 1, ..., n - 1] 中不同元素的数目。

返回 nums 的 不同元素数目差 数组。

注意 nums[i, ..., j] 表示 nums 的一个从下标 i 开始到下标 j 结束的子数组（包含下标 i 和 j 对应元素）。特别需要说明的是，如果 i > j ，则 nums[i, ..., j] 表示一个空子数组。


```
use std::collections::HashSet;

impl Solution {
    pub fn distinct_difference_array(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();

        // suf[i] = nums[i..n] 中不同元素的个数（suf[n] = 0 表示空后缀）
        let mut suf = vec![0i32; n + 1];
        let mut seen = HashSet::new();
        for i in (0..n).rev() {
            seen.insert(nums[i]);
            suf[i] = seen.len() as i32;
        }

        // 从左到右维护前缀的不同元素个数，与后缀相减得到答案
        let mut ans = Vec::with_capacity(n);
        seen.clear();
        for i in 0..n {
            seen.insert(nums[i]);
            ans.push(seen.len() as i32 - suf[i + 1]);
        }

        ans
    }
}
```
