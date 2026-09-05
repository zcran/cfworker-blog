---
title: "leetcode-前缀和64"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 分割数组的方案数

给你一个下标从 0 开始长度为 n 的整数数组 nums 。
如果以下描述为真，那么 nums 在下标 i 处有一个 合法的分割 ：

前 i + 1 个元素的和 大于等于 剩下的 n - i - 1 个元素的和。

下标 i 的右边 至少有一个 元素，也就是说下标 i 满足 0 <= i < n - 1 。

请你返回 nums 中的 合法分割 方案数。


```
impl Solution {
    pub fn ways_to_split_array(nums: Vec<i32>) -> i32 {
        let n = nums.len();

        // 计算数组总和
        let total_sum: i64 = nums.iter().map(|&x| x as i64).sum();

        let mut left_sum: i64 = 0;
        let mut count = 0;

        // 枚举分割点 i，使得左边包含 nums[0..=i]，右边包含 nums[i+1..]
        for i in 0..n - 1 {
            left_sum += nums[i] as i64;
            let right_sum = total_sum - left_sum;

            if left_sum >= right_sum {
                count += 1;
            }
        }

        count
    }
}
```
