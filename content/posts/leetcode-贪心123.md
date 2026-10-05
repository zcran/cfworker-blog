---
title: "leetcode-贪心123"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 合并后数组中的最大元素

给你一个下标从 0 开始、由正整数组成的数组 nums 。

你可以在数组上执行下述操作 任意 次：

选中一个同时满足 0 <= i < nums.length - 1 和 nums[i] <= nums[i + 1] 的下标 i 。将元素 nums[i + 1] 替换为 nums[i] + nums[i + 1] ，并从数组中删除元素 nums[i] 。

返回你可以从最终数组中获得的 最大 元素的值。


```
impl Solution {
    pub fn max_array_value(nums: Vec<i32>) -> i64 {
        // 从右往左合并：若 nums[i] <= 已累积的和，则可把它并入，
        // 否则重置为 nums[i]（因为无法与右侧合并，只能保留自身）。
        let mut sum = nums[nums.len() - 1] as i64;
        for &x in nums[..nums.len() - 1].iter().rev() {
            let v = x as i64;
            sum = if v <= sum { v + sum } else { v };
        }
        sum
    }
}
```
