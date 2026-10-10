---
title: "leetcode-数学62"
date: 2026-10-07T20:39:32+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 判断是否可以赢得数字游戏

给你一个 正整数 数组 nums。

Alice 和 Bob 正在玩游戏。在游戏中，Alice 可以从 nums 中选择所有个位数 或 所有两位数，剩余的数字归 Bob 所有。如果 Alice 所选数字之和 严格大于 Bob 的数字之和，则 Alice 获胜。

如果 Alice 能赢得这场游戏，返回 true；否则，返回 false。


```
impl Solution {
    pub fn can_alice_win(nums: Vec<i32>) -> bool {
        // 统计个位数之和与两位数之和。
        // Alice 会选较大的那组，只有两组和相等时她才无法获胜。
        let (single, double) = nums.iter().fold((0, 0), |(s, d), &x| {
            if x < 10 { (s + x, d) } else { (s, d + x) }
        });

        single != double
    }
}
```
