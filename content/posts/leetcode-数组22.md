---
title: "leetcode-数组22"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 重新排列数组

给你一个数组 nums ，数组中有 2n 个元素，按 [x1,x2,...,xn,y1,y2,...,yn] 的格式排列。

请你将数组按 [x1,y1,x2,y2,...,xn,yn] 格式重新排列，返回重排后的数组。


```
impl Solution {
    /// 将数组 `[x1,...,xn,y1,...,yn]` 重排为 `[x1,y1,x2,y2,...,xn,yn]`。
    ///
    /// 前半段 `nums[0..n]` 是 x 部分，后半段 `nums[n..2n]` 是 y 部分，
    /// 交替写入结果数组即可。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（结果数组）。
    pub fn shuffle(nums: Vec<i32>, n: i32) -> Vec<i32> {
        let n = n as usize;
        let mut result = Vec::with_capacity(2 * n);

        for i in 0..n {
            result.push(nums[i]);
            result.push(nums[n + i]);
        }

        result
    }
}
```
