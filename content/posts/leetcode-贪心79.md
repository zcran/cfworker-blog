---
title: "leetcode-贪心79"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 消灭怪物的最大数量

你正在玩一款电子游戏，在游戏中你需要保护城市免受怪物侵袭。给定一个 下标从 0 开始 且大小为 n 的整数数组 dist ，其中 dist[i] 是第 i 个怪物与城市的 初始距离（单位：千米）。

怪物以 恒定 的速度走向城市。每个怪物的速度都以一个长度为 n 的整数数组 speed 表示，其中 speed[i] 是第 i 个怪物的速度（单位：千米/分）。

你有一种武器，一旦充满电，就可以消灭 一个 怪物。但是，武器需要 一分钟 才能充电。武器在游戏开始时是充满电的状态，怪物从 第 0 分钟 时开始移动。

一旦任一怪物到达城市，你就输掉了这场游戏。如果某个怪物 恰好 在某一分钟开始时到达城市（距离表示为0），这也会被视为 输掉 游戏，在你可以使用武器之前，游戏就会结束。

返回在你输掉游戏前可以消灭的怪物的 最大 数量。如果你可以在所有怪物到达城市前将它们全部消灭，返回  n 。


```
impl Solution {
    pub fn eliminate_maximum(dist: Vec<i32>, speed: Vec<i32>) -> i32 {
        // 每个怪物到达城市的分钟数（向上取整）：(d - 1) / s + 1
        let mut arrival: Vec<i32> = dist
            .iter()
            .zip(&speed)
            .map(|(&d, &s)| (d - 1) / s + 1)
            .collect();

        // 按到达时间升序，优先消灭最先到达的怪物
        arrival.sort_unstable();

        // 第 i 个怪物需在第 i 分钟前被消灭（武器第 i 分钟可用，共 i+1 次射击机会）
        // 只要 arrival[i] > i 就能在第 i 分钟射杀它；一旦 arrival[i] <= i 则来不及
        let mut killed = 0;
        while killed < arrival.len() && arrival[killed] > killed as i32 {
            killed += 1;
        }
        killed as i32
    }
}
```
