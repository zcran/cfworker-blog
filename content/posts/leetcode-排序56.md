---
title: "leetcode-排序56"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 既不是最小值也不是最大值

给你一个整数数组 nums ，数组由 不同正整数 组成，请你找出并返回数组中 任一 既不是 最小值 也不是 最大值 的数字，如果不存在这样的数字，返回 -1 。

返回所选整数。


```
impl Solution {
    /// 返回数组中任一既不是最小值也不是最大值的元素，不存在则返回 -1。
    ///
    /// 思路：
    /// 数组由不同正整数组成，只要长度 >= 3，取任意三个元素排序后，
    /// 中间那个一定既不是最小值也不是最大值。
    pub fn find_non_min_or_max(mut nums: Vec<i32>) -> i32 {
        if nums.len() < 3 {
            return -1;
        }
        nums[..3].sort_unstable();
        nums[1]
    }
}
```
