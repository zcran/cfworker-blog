---
title: "leetcode-滑动窗口87"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 或值至少为 K 的最短子数组 I

给你一个 非负 整数数组 nums 和一个整数 k 。

如果一个数组中所有元素的按位或运算 OR 的值 至少 为 k ，那么我们称这个数组是 特别的 。

请你返回 nums 中 最短特别非空 子数组的长度，如果特别子数组不存在，那么返回 -1 。


```
impl Solution {
    pub fn minimum_subarray_length(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let mut ans = i32::MAX;

        // 枚举所有子数组的起始位置
        for start in 0..n {
            let mut or_val = 0;
            // 向右扩展，计算 OR 值
            for end in start..n {
                or_val |= nums[end];
                // 一旦满足条件，更新答案并提前终止（更长的子数组不会更优）
                if or_val >= k {
                    ans = ans.min((end - start + 1) as i32);
                    break;
                }
            }
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```
