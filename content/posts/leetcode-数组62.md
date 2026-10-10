---
title: "leetcode-数组62"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 容量最小的箱子

给你一个整数数组 capacity，其中 capacity[i] 表示第 i 个箱子的容量，以及一个整数 itemSize，表示一个物品的大小。

如果第 i 个箱子的容量满足 capacity[i] >= itemSize，那么该箱子可以存放该物品。

要求返回可以存放该物品的容量 最小 的箱子的下标。如果有多个这样的箱子，返回下标 最小 的一个。

如果没有任何箱子可以存放该物品，则返回 -1。


```
impl Solution {
    pub fn minimum_index(capacity: Vec<i32>, item_size: i32) -> i32 {
        capacity
            .iter()
            .enumerate()
            // 只考虑能容纳物品的箱子，按 (容量, 下标) 取最小值
            .filter(|&(_, &c)| c >= item_size)
            .min_by_key(|&(i, &c)| (c, i))
            .map_or(-1, |(i, _)| i as i32)
    }
}
```
