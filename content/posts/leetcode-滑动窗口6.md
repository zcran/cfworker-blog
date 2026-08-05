---
title: "leetcode-滑动窗口6"
date: 2026-07-18T11:02:29+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 等差数列划分

如果一个数列 至少有三个元素 ，并且任意两个相邻元素之差相同，则称该数列为等差数列。

例如，[1,3,5,7,9]、[7,7,7,7] 和 [3,-1,-5,-9] 都是等差数列。

给你一个整数数组 nums ，返回数组 nums 中所有为等差数组的 子数组 个数。

子数组 是数组中的一个连续序列。




```
impl Solution {
    pub fn number_of_arithmetic_slices(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n < 3 {
            return 0;
        }

        let mut total = 0;      // 等差数列子数组总数
        let mut count = 0;      // 以当前位置结尾的连续等差数列个数
        let mut prev_diff = nums[1] - nums[0];

        for i in 2..n {
            let curr_diff = nums[i] - nums[i - 1];
            if curr_diff == prev_diff {
                count += 1;     // 新增 count 个以 i 结尾的等差数列
                total += count;
            } else {
                count = 0;      // 等差中断，重置计数
                prev_diff = curr_diff;
            }
        }

        total
    }
}
```
