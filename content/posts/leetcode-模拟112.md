---
title: "leetcode-模拟112"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 求出硬币游戏的赢家

给你两个 正 整数 x 和 y ，分别表示价值为 75 和 10 的硬币的数目。

Alice 和 Bob 正在玩一个游戏。每一轮中，Alice 先进行操作，Bob 后操作。每次操作中，玩家需要拿走价值 总和 为 115 的硬币。如果一名玩家无法执行此操作，那么这名玩家 输掉 游戏。

两名玩家都采取 最优 策略，请你返回游戏的赢家。


```
impl Solution {
    pub fn winning_player(x: i32, y: i32) -> String {
        // 每轮需要凑115 = 75*1 + 10*4，即消耗1个75硬币和4个10硬币
        // 最多能进行的轮数由两者中更紧缺的资源决定
        let rounds = x.min(y / 4);
        // 奇数轮：Alice执行最后一轮，Bob无法行动，Alice胜
        // 偶数轮：Bob执行最后一轮，Alice无法行动，Bob胜
        (if rounds % 2 == 1 { "Alice" } else { "Bob" }).to_string()
    }
}
```
