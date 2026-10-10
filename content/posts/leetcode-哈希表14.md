---
title: "leetcode-哈希表14"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 分糖果

Alice 有 n 枚糖，其中第 i 枚糖的类型为 candyType[i] 。Alice 注意到她的体重正在增长，所以前去拜访了一位医生。

医生建议 Alice 要少摄入糖分，只吃掉她所有糖的 n / 2 即可（n 是一个偶数）。Alice 非常喜欢这些糖，她想要在遵循医生建议的情况下，尽可能吃到最多不同种类的糖。

给你一个长度为 n 的整数数组 candyType ，返回： Alice 在仅吃掉 n / 2 枚糖的情况下，可以吃到糖的 最多 种类数。


```
use std::collections::HashSet;

impl Solution {
    pub fn distribute_candies(candy_type: Vec<i32>) -> i32 {
        // 最多能吃的种类数 = min(总种类数, n/2)。
        // 种类数即去重后的元素个数，n/2 是允许吃的数量上限。
        let kinds = candy_type.iter().collect::<HashSet<_>>().len();
        kinds.min(candy_type.len() / 2) as i32
    }
}
```
