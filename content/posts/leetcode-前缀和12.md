---
title: "leetcode-前缀和12"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 和可被 K 整除的子数组

给定一个整数数组 nums 和一个整数 k ，返回其中元素之和可被 k 整除的非空 子数组 的数目。

子数组 是数组中 连续 的部分。


```
impl Solution {
    pub fn subarrays_div_by_k(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        // 前缀和取模，范围 0..k-1
        let mut cnt = vec![0; k];
        let mut sum = 0;
        let mut ans = 0;

        // 初始前缀和为 0，出现一次
        cnt[0] = 1;

        for x in nums {
            // 计算前缀和模 k，处理负数
            sum = ((sum + x) % k as i32 + k as i32) % k as i32;
            let idx = sum as usize;
            // 当前前缀和之前出现次数即为以当前位置结尾的可整除子数组数
            ans += cnt[idx];
            cnt[idx] += 1;
        }

        ans
    }
}
```
