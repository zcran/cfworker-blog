---
title: "leetcode-堆52"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 执行 K 次操作后的最大分数

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。你的 起始分数 为 0 。

在一步 操作 中：

1. 选出一个满足 0 <= i < nums.length 的下标 i ，
2. 将你的 分数 增加 nums[i] ，并且
3. 将 nums[i] 替换为 ceil(nums[i] / 3) 。

返回在 恰好 执行 k 次操作后，你可能获得的最大分数。

向上取整函数 ceil(val) 的结果是大于或等于 val 的最小整数。


```
use std::collections::BinaryHeap;

impl Solution {
    pub fn max_kelements(nums: Vec<i32>, k: i32) -> i64 {
        // 原地堆化为最大堆，O(n)
        let mut heap = BinaryHeap::from(nums);
        let mut score = 0i64;

        for _ in 0..k {
            let mx = heap.pop().unwrap();
            score += mx as i64;
            // ceil(mx / 3) 的整数技巧，避免浮点
            heap.push((mx + 2) / 3);
        }

        score
    }
}
```
