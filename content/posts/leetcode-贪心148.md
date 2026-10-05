---
title: "leetcode-贪心148"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 高度互不相同的最大塔高和

给你一个数组 maximumHeight ，其中 maximumHeight[i] 表示第 i 座塔可以达到的 最大 高度。

你的任务是给每一座塔分别设置一个高度，使得：

1. 第 i 座塔的高度是一个正整数，且不超过 maximumHeight[i] 。
2. 所有塔的高度互不相同。

请你返回设置完所有塔的高度后，可以达到的 最大 总高度。如果没有合法的设置，返回 -1 。


```
impl Solution {
    pub fn maximum_total_sum(mut maximum_height: Vec<i32>) -> i64 {
        // 贪心：降序排序后，从高到低依次分配高度，
        // 每座塔的高度取其上限与"前一座塔高度 - 1"中的较小值，
        // 以保证高度严格递减且互不相同。若某座塔高度 <= 0 则无解。
        maximum_height.sort_unstable_by(|a, b| b.cmp(a));

        let mut total = 0i64;
        let mut prev = i32::MAX; // 前一座塔分配的高度

        for h in maximum_height {
            let cur = h.min(prev - 1);
            if cur <= 0 {
                return -1;
            }
            total += cur as i64;
            prev = cur;
        }
        total
    }
}
```
