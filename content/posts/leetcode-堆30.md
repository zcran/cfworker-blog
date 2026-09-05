---
title: "leetcode-堆30"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 移除石子的最大得分

你正在玩一个单人游戏，面前放置着大小分别为 a、b 和 c 的 三堆 石子。

每回合你都要从两个 不同的非空堆 中取出一颗石子，并在得分上加 1 分。当存在 两个或更多 的空堆时，游戏停止。

给你三个整数 a 、b 和 c ，返回可以得到的 最大分数 。




```
impl Solution {
    pub fn maximum_score(a: i32, b: i32, c: i32) -> i32 {
        let total = a + b + c;
        let max_pile = a.max(b).max(c);
        (total / 2).min(total - max_pile)
    }
}
```
