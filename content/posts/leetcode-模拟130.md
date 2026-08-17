---
title: "leetcode-模拟130"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 计算交替和

给你一个整数数组 nums。

交替和 定义为：将 nums 中偶数下标位置的元素 相加 ，减去 奇数下标位置的元素。即：nums[0] - nums[1] + nums[2] - nums[3]...

返回表示 nums 的交替和的整数。


```
impl Solution {
    pub fn alternating_sum(nums: Vec<i32>) -> i32 {
        nums.into_iter()
            .zip([1, -1].iter().cycle())
            .fold(0, |sum, (num, &factor)| sum + num * factor)
    }
}
```
