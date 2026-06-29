---
title: "leetcode-单调栈46"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 非递减数组的最大长度


给你一个整数数组 nums。在一次操作中，你可以选择一个子数组，并将其替换为一个等于该子数组 最大值 的单个元素。

返回经过零次或多次操作后，数组仍为 非递减 的情况下，数组 可能的最大长度。

子数组 是数组中一个连续、非空 的元素序列。


```
impl Solution {
    /// 贪心：保留所有 >= 当前最大值的元素
    pub fn maximum_possible_size(nums: Vec<i32>) -> i32 {
        let mut max = 0;
        nums.iter().filter(|&&x| {
            let ok = x >= max;
            if ok { max = x; }
            ok
        }).count() as i32
    }
}
```
