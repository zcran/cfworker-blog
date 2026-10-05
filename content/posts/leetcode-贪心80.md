---
title: "leetcode-贪心80"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 新增的最少台阶数

给你一个 严格递增 的整数数组 rungs ，用于表示梯子上每一台阶的 高度 。当前你正站在高度为 0 的地板上，并打算爬到最后一个台阶。

另给你一个整数 dist 。每次移动中，你可以到达下一个距离你当前位置（地板或台阶）不超过 dist 高度的台阶。当然，你也可以在任何正 整数 高度处插入尚不存在的新台阶。

返回爬到最后一阶时必须添加到梯子上的 最少 台阶数。


```
impl Solution {
    pub fn add_rungs(rungs: Vec<i32>, dist: i32) -> i32 {
        let mut added = 0;
        let mut current = 0;

        // 遍历每一阶，计算与当前位置的高度差，所需的额外台阶数为 (d - 1) / dist
        for h in rungs {
            added += (h - current - 1) / dist;
            current = h;
        }

        added
    }
}
```
