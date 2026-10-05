---
title: "leetcode-二分查找59"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 青蛙过河 II

给你一个下标从 0 开始的整数数组 stones ，数组中的元素 严格递增 ，表示一条河中石头的位置。

一只青蛙一开始在第一块石头上，它想到达最后一块石头，然后回到第一块石头。同时每块石头 至多 到达 一次。

一次跳跃的 长度 是青蛙跳跃前和跳跃后所在两块石头之间的距离。

更正式的，如果青蛙从 stones[i] 跳到 stones[j] ，跳跃的长度为 |stones[i] - stones[j]| 。

一条路径的 代价 是这条路径里的 最大跳跃长度 。

请你返回这只青蛙的 最小代价 。


```
impl Solution {
    pub fn max_jump(stones: Vec<i32>) -> i32 {
        // 青蛙去程和回程各经过每块石头一次，可视为把石头序列分成两条交错路径。
        // 任意相邻两块石头间的跨越（间隔为 2）是不可避免的最小代价下界，
        // 而该下界可以通过交替选择石头同时达到，因此答案为所有间隔为 2 的距离最大值。
        let mut ans = stones[1] - stones[0];
        for i in 2..stones.len() {
            ans = ans.max(stones[i] - stones[i - 2]);
        }
        ans
    }
}
```
