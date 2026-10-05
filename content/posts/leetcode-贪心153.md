---
title: "leetcode-贪心153"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 删除后的最大子数组元素和

给你一个整数数组 nums 。

你可以从数组 nums 中删除任意数量的元素，但不能将其变为 空 数组。执行删除操作后，选出 nums 中满足下述条件的一个子数组：

1. 子数组中的所有元素 互不相同 。
2. 最大化 子数组的元素和。

返回子数组的 最大元素和 。

子数组 是数组的一个连续、非空 的元素序列。


```
impl Solution {
    pub fn max_sum(nums: Vec<i32>) -> i32 {
        // 删除操作可以把数组"修剪"成任意子数组，
        // 因此问题等价于：从 nums 中选一个元素互不相同的集合，使其和最大。
        // 贪心：所有正数都选（重复的正数只算一次），负数一律不选。
        let mut seen = [false; 201]; // nums[i] ∈ [-100, 100]，偏移 +100
        let mut sum = 0;
        let mut has_positive = false;

        for &x in &nums {
            if x > 0 {
                let idx = (x + 100) as usize;
                if !seen[idx] {
                    seen[idx] = true;
                    sum += x;
                    has_positive = true;
                }
            }
        }

        // 若没有正数，只能选最大的那个非正数
        if has_positive {
            sum
        } else {
            *nums.iter().max().unwrap()
        }
    }
}
```
