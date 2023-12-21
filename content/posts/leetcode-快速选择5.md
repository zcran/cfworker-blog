---
title: "Leetcode 快速选择5"
date: 2023-12-19T15:49:31+08:00
tags: ["leetcode", "快速选择"]
draft: false
---

## 最接近原点的 K 个点

给定一个数组 points ，其中 points[i] = [x⌄i, y⌄i] 表示 X-Y 平面上的一个点，并且是一个整数 k ，返回离原点 (0,0) 最近的 k 个点。

这里，平面上两点之间的距离是 欧几里德距离（ √(x⌄1 - x⌄2)2 + (y⌄1 - y⌄2)2 ）。

你可以按 任何顺序 返回答案。除了点坐标的顺序之外，答案 确保 是 唯一 的。

```
impl Solution {
    pub fn k_closest(points: Vec<Vec<i32>>, k: i32) -> Vec<Vec<i32>> {
        let mut ans = points;
        ans.sort_unstable_by_key(|a| a[0] * a[0] + a[1] * a[1]);
        ans.truncate(k as usize); // 保留第一个 k 元素，然后丢弃其余的元素
        ans
    }
}
```