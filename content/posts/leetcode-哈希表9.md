---
title: "leetcode-哈希表9"
date: 2026-10-09T20:07:55+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 找到所有数组中消失的数字

给你一个含 n 个整数的数组 nums ，其中 nums[i] 在区间 [1, n] 内。请你找出所有在 [1, n] 范围内但没有出现在 nums 中的数字，并以数组的形式返回结果。


```
impl Solution {
    pub fn find_disappeared_numbers(mut nums: Vec<i32>) -> Vec<i32> {
        // 原地标记：值 v ∈ [1, n] 对应下标 v-1，把它标记为负数。
        // 遍历结束后，仍为正数的下标 i 说明值 i+1 未出现过。
        // 相比额外计数数组，省去 O(n) 空间。
        let n = nums.len();
        for i in 0..n {
            let idx = (nums[i].abs() - 1) as usize;
            if nums[idx] > 0 {
                nums[idx] = -nums[idx];
            }
        }
        (0..n)
            .filter(|&i| nums[i] > 0)
            .map(|i| (i + 1) as i32)
            .collect()
    }
}
```
