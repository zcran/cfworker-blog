---
title: "Leetcode 基数排序2"
date: 2023-12-19T15:14:04+08:00
tags: ["leetcode", "基数排序"]
draft: false
---

## 最大间距

给定一个无序的数组 nums，返回 数组在排序之后，相邻元素之间最大的差值 。如果数组元素个数小于 2，则返回 0 。

您必须编写一个在「线性时间」内运行并使用「线性额外空间」的算法。

```
impl Solution {
    pub fn maximum_gap(mut nums: Vec<i32>) -> i32 {
        let mut buckets = vec![vec![];10];

        for i in 0..10 {
            nums.iter().for_each(|&num| buckets[((num / 10_i32.pow(i)) % 10) as usize].push(num));
            buckets.iter().flat_map(|bucket| bucket.iter()).zip(nums.iter_mut()).for_each(|(&a, b)| *b = a);
            buckets.iter_mut().for_each(|bucket| bucket.clear());
        }

        nums.windows(2).map(|w| w[1] - w[0]).max().unwrap_or(0)
    }
}
```