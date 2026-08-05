---
title: "leetcode-枚举54"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 将数组分成最小总代价的子数组 I

给你一个长度为 n 的整数数组 nums 。

一个数组的 代价 是它的 第一个 元素。比方说，[1,2,3] 的代价是 1 ，[3,4,1] 的代价是 3 。

你需要将 nums 分成 3 个 连续且没有交集 的子数组。

请你返回这些子数组的 最小 代价 总和 。


```
impl Solution {
    pub fn minimum_cost(mut nums: Vec<i32>) -> i32 {
        // 第一个子数组的第一个元素必须是 nums[0]
        // 只需要从剩余元素中选出最小的两个作为另外两个子数组的首元素
        // 因此总代价 = nums[0] + 剩余元素中最小的两个之和

        if nums.len() <= 3 {
            return nums.iter().sum();
        }

        // 对 nums[1..] 部分排序，取出最小的两个
        // 使用 split_at_mut 避免同时可变和不可变借用
        let (first, rest) = nums.split_at_mut(1);
        rest.sort_unstable();

        first[0] + rest[0] + rest[1]
    }
}
```
