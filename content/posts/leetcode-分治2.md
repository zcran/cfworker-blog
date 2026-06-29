---
title: "leetcode-分治2"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 最大子数组和


给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

子数组是数组中的一个连续部分。

```
impl Solution {
    pub fn max_sub_array(nums: Vec<i32>) -> i32 {
        let mut ans = i32::MIN;
        let mut min_pre_sum = 0;
        let mut pre_sum = 0;
        for x in nums {
            pre_sum += x; // 当前的前缀和
            ans = ans.max(pre_sum - min_pre_sum); // 减去前缀和的最小值
            min_pre_sum = min_pre_sum.min(pre_sum); // 维护前缀和的最小值
        }
        ans
    }
}
```
