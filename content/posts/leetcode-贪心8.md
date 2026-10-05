---
title: "leetcode-贪心8"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 按要求补齐数组

给定一个已排序的正整数数组 nums ，和一个正整数 n 。从 [1, n] 区间内选取任意个数字补充到 nums 中，使得 [1, n] 区间内的任何数字都可以用 nums 中某几个数字的和来表示。

请返回 满足上述要求的最少需要补充的数字个数 。


```
impl Solution {
    pub fn min_patches(nums: Vec<i32>, n: i32) -> i32 {
        // 贪心：用 s 表示当前能连续覆盖 [0, s) 的所有数字
        // 若 nums[i] <= s，则可将覆盖范围扩展到 s + nums[i]
        // 否则说明 [s, ...] 无法覆盖，必须补充 s，覆盖范围翻倍为 2s
        let (mut patches, mut s, mut i) = (0, 1i64, 0);
        let target = n as i64;

        while s <= target {
            if i < nums.len() && (nums[i] as i64) <= s {
                s += nums[i] as i64;
                i += 1;
            } else {
                s *= 2;
                patches += 1;
            }
        }

        patches
    }
}
```
