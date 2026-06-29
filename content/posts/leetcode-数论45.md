---
title: "leetcode-数论45"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 质数的最大距离

给你一个整数数组 nums。

返回两个（不一定不同的）质数在 nums 中 下标 的 最大距离。


```
impl Solution {
    pub fn maximum_prime_difference(nums: Vec<i32>) -> i32 {
        /// 判断一个数是否为质数
        fn is_prime(num: i32) -> bool {
            // 小于2的数不是质数
            if num < 2 {
                return false;
            }
            // 只需检查到 sqrt(num)
            let limit = (num as f64).sqrt() as i32;
            for divisor in 2..=limit {
                if num % divisor == 0 {
                    return false; // 有因子，不是质数
                }
            }
            true // 没有因子，是质数
        }

        // 使用双指针从两端向中间查找
        let mut left = 0;
        let mut right = nums.len() - 1;

        // 从左向右找到第一个质数
        while !is_prime(nums[left]) {
            left += 1;
        }

        // 从右向左找到最后一个质数
        while !is_prime(nums[right]) {
            right -= 1;
        }

        // 计算最大距离（两个质数下标之差）
        (right - left) as i32
    }
}
```
