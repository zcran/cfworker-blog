---
title: "leetcode-模拟49"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 基于排列构建数组

给你一个 从 0 开始的排列 nums（下标也从 0 开始）。请你构建一个 同样长度 的数组 ans ，其中，对于每个 i（0 <= i < nums.length），都满足 ans[i] = nums[nums[i]] 。返回构建好的数组 ans 。

从 0 开始的排列 nums 是一个由 0 到 nums.length - 1（0 和 nums.length - 1 也包含在内）的不同整数组成的数组。


```
impl Solution {
    pub fn build_array(nums: Vec<i32>) -> Vec<i32> {
        nums.iter().map(|&x| nums[x as usize]).collect()
    }
}
```
