---
title: "leetcode-排序9"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 数组中重复的数据

给你一个长度为 n 的整数数组 nums ，其中 nums 的所有整数都在范围 [1, n] 内，且每个整数出现 最多两次 。请你找出所有出现 两次 的整数，并以数组形式返回。

你必须设计并实现一个时间复杂度为 O(n) 且仅使用常量额外空间（不包括存储输出所需的空间）的算法解决此问题。


```
impl Solution {
    /// 找出所有出现两次的整数
    ///
    /// 思路：数组元素均在 [1, n] 内，可将元素值 |x| 当作下标，
    /// 通过「取负」标记该下标已访问过：首次访问置负，再次访问即为重复。
    /// 取绝对值是为了处理已被标记为负的元素。
    /// 时间 O(n)，额外空间 O(1)（不含输出）。
    pub fn find_duplicates(mut nums: Vec<i32>) -> Vec<i32> {
        let mut ans = Vec::new();

        for i in 0..nums.len() {
            // 元素值映射到下标 [0, n-1]；abs 兼容已被标记为负的元素
            let idx = (nums[i].abs() - 1) as usize;
            if nums[idx] < 0 {
                // 该下标已被标记过，说明 idx+1 出现两次
                ans.push(idx as i32 + 1);
            } else {
                // 首次访问：置负作为标记
                nums[idx] = -nums[idx];
            }
        }

        ans
    }
}
```
