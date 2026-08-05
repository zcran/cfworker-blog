---
title: "leetcode-枚举96"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计主导元素下标数

给你一个长度为 n 的整数数组 nums。

当下标 i 满足以下条件时，该下标处的元素被称为 主导元素：nums[i] > average(nums[i + 1], nums[i + 2], ..., nums[n - 1])

你的任务是统计数组中 主导元素 的下标数。

平均值 是指一组数的总和除以该组数的个数得到的值。

注意：数组的 最右边元素 不算作 主导元素 。


```
impl Solution {
    pub fn dominant_indices(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut suffix_sum = 0;
        let mut ans = 0;

        // 从倒数第二个元素开始向前遍历
        // 因为最右边的元素不是主导元素
        for i in (0..n - 1).rev() {
            suffix_sum += nums[i + 1];
            // 避免浮点运算：nums[i] > suffix_sum / (n - 1 - i)
            // 等价于 nums[i] * (n - 1 - i) > suffix_sum
            if nums[i] * (n - 1 - i) as i32 > suffix_sum {
                ans += 1;
            }
        }

        ans
    }
}
```
