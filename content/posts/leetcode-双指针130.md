---
title: "leetcode-双指针130"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 将 0 移到末尾的最少交换次数

给你一个整数数组 nums 。

在一步操作中，你可以选择任意两个 不同 的下标 i 和 j 并交换 nums[i] 和 nums[j] 。

返回将所有 0 移动到数组末尾所需的 最少 操作次数。


```
impl Solution {
    pub fn minimum_swaps(nums: Vec<i32>) -> i32 {
        // 非零元素个数
        let cnt = nums.iter().filter(|&&x| x != 0).count();
        // 前 cnt 个位置中 0 的个数即为需要交换的次数
        nums[..cnt].iter().filter(|&&x| x == 0).count() as i32
    }
}
```
