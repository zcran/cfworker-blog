---
title: "leetcode-双指针89"
date: 2026-09-05T09:53:06+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 运动员和训练师的最大匹配数

给你一个下标从 0 开始的整数数组 players ，其中 players[i] 表示第 i 名运动员的 能力 值，同时给你一个下标从 0 开始的整数数组 trainers ，其中 trainers[j] 表示第 j 名训练师的 训练能力值 。

如果第 i 名运动员的能力值 小于等于 第 j 名训练师的能力值，那么第 i 名运动员可以 匹配 第 j 名训练师。除此以外，每名运动员至多可以匹配一位训练师，每位训练师最多可以匹配一位运动员。

请你返回满足上述要求 players 和 trainers 的 最大 匹配数。


```
impl Solution {
    pub fn match_players_and_trainers(mut players: Vec<i32>, mut trainers: Vec<i32>) -> i32 {
        players.sort_unstable();
        trainers.sort_unstable();

        let mut j = 0;       // 训练师指针
        let mut matches = 0; // 匹配计数

        for &p in &players {
            // 跳过能力值不足的训练师
            while j < trainers.len() && trainers[j] < p {
                j += 1;
            }
            // 没有可用训练师，结束
            if j == trainers.len() {
                break;
            }
            // 匹配成功，双方指针后移
            matches += 1;
            j += 1;
        }

        matches
    }
}
```
