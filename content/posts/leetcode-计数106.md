---
title: "leetcode-计数106"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 求出胜利玩家的数目

给你一个整数 n ，表示在一个游戏中的玩家数目。同时给你一个二维整数数组 pick ，其中 pick[i] = [xi, yi] 表示玩家 xi 获得了一个颜色为 yi 的球。

如果玩家 i 获得的球中任何一种颜色球的数目 严格大于 i 个，那么我们说玩家 i 是胜利玩家。换句话说：

· 如果玩家 0 获得了任何的球，那么玩家 0 是胜利玩家。
· 如果玩家 1 获得了至少 2 个相同颜色的球，那么玩家 1 是胜利玩家。
· ...
· 如果玩家 i 获得了至少 i + 1 个相同颜色的球，那么玩家 i 是胜利玩家。

请你返回游戏中 胜利玩家 的数目。

注意，可能有多个玩家是胜利玩家。


```
impl Solution {
    pub fn winning_player_count(n: i32, pick: Vec<Vec<i32>>) -> i32 {
        // 每个玩家每种颜色的计数，颜色范围 0..=10
        let mut cnt = vec![[0; 11]; n as usize];

        for p in &pick {
            cnt[p[0] as usize][p[1] as usize] += 1;
        }

        // 统计胜利玩家：存在某种颜色球数严格大于玩家编号
        cnt.iter()
            .enumerate()
            .filter(|(i, colors)| colors.iter().any(|&c| c > *i))
            .count() as i32
    }
}
```
