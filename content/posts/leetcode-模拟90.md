---
title: "leetcode-模拟90"
date: 2026-08-08T11:31:14+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 保龄球游戏的获胜者

给你两个下标从 0 开始的整数数组 player1 和 player2 ，分别表示玩家 1 和玩家 2 击中的瓶数。

保龄球比赛由 n 轮组成，每轮的瓶数恰好为 10 。

假设玩家在第 i 轮中击中 xi 个瓶子。玩家第 i 轮的价值为：

如果玩家在该轮的前两轮的任何一轮中击中了 10 个瓶子，则为 2xi 。
否则，为 xi 。

玩家的得分是其 n 轮价值的总和。

返回

如果玩家 1 的得分高于玩家 2 的得分，则为 1 ；
如果玩家 2 的得分高于玩家 1 的得分，则为 2 ；
如果平局，则为 0 。


```
impl Solution {
    pub fn is_winner(player1: Vec<i32>, player2: Vec<i32>) -> i32 {
        // 计算单个玩家的得分：前两轮有全中则本轮双倍
        let score = |pins: &Vec<i32>| -> i32 {
            let mut sum = 0;
            for (i, &x) in pins.iter().enumerate() {
                let double = (i > 0 && pins[i - 1] == 10) || (i > 1 && pins[i - 2] == 10);
                sum += if double { 2 * x } else { x };
            }
            sum
        };

        match score(&player1).cmp(&score(&player2)) {
            std::cmp::Ordering::Greater => 1,
            std::cmp::Ordering::Less => 2,
            std::cmp::Ordering::Equal => 0,
        }
    }
}
```
