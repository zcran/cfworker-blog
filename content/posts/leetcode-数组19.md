---
title: "leetcode-数组19"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 拥有最多糖果的孩子

有 n 个有糖果的孩子。给你一个数组 candies，其中 candies[i] 代表第 i 个孩子拥有的糖果数目，和一个整数 extraCandies 表示你所有的额外糖果的数量。

返回一个长度为 n 的布尔数组 result，如果把所有的 extraCandies 给第 i 个孩子之后，他会拥有所有孩子中 最多 的糖果，那么 result[i] 为 true，否则为 false。

注意，允许有多个孩子同时拥有 最多 的糖果数目。


```
impl Solution {
    /// 判断每个孩子获得全部额外糖果后，能否达到或超过当前最大值。
    ///
    /// 先求出当前最大糖果数 `max_candies`，则第 i 个孩子满足
    /// `candies[i] + extra_candies >= max_candies` 时结果为 true。
    /// 等价于 `candies[i] >= max_candies - extra_candies`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（结果数组）。
    pub fn kids_with_candies(candies: Vec<i32>, extra_candies: i32) -> Vec<bool> {
        let threshold = *candies.iter().max().unwrap() - extra_candies;
        candies.into_iter().map(|c| c >= threshold).collect()
    }
}
```
