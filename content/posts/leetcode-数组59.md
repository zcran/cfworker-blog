---
title: "leetcode-数组59"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 最长斐波那契子数组

给你一个由 正 整数组成的数组 nums。

斐波那契 数组是一个连续序列，其中第三项及其后的每一项都等于这一项前面两项之和。

返回 nums 中最长 斐波那契 子数组的长度。

注意: 长度为 1 或 2 的子数组总是 斐波那契 的。

子数组 是数组中 非空 的连续元素序列。


```
impl Solution {
    pub fn longest_subarray(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        let mut ans = 2; // 长度 1 或 2 的子数组总是斐波那契的

        // 枚举斐波那契子数组的左端点 i
        for i in 0..n {
            let mut j = i + 2;
            // 尽可能向右延伸，满足 nums[j] == nums[j-1] + nums[j-2]
            while j < n && nums[j] == nums[j - 1] + nums[j - 2] {
                j += 1;
            }
            // [i, j-1] 是斐波那契子数组
            ans = ans.max(j - i);
        }

        ans as i32
    }
}
```
