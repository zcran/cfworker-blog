---
title: "leetcode-枚举50"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 找出峰值

给你一个下标从 0 开始的数组 mountain 。你的任务是找出数组 mountain 中的所有 峰值。

以数组形式返回给定数组中 峰值 的下标，顺序不限 。

注意：

峰值 是指一个严格大于其相邻元素的元素。
数组的第一个和最后一个元素 不 是峰值。


```
impl Solution {
    pub fn find_peaks(mountain: Vec<i32>) -> Vec<i32> {
        // 峰值必须满足：mountain[i-1] < mountain[i] > mountain[i+1]
        // 首尾元素不可能是峰值，所以从索引 1 到 n-2 遍历
        mountain
            .windows(3)
            .enumerate()
            .filter_map(|(i, window)| {
                // window 包含 [mountain[i], mountain[i+1], mountain[i+2]]
                // 当前检查的峰值索引为 i+1
                if window[0] < window[1] && window[1] > window[2] {
                    Some((i + 1) as i32)
                } else {
                    None
                }
            })
            .collect()
    }
}
```
