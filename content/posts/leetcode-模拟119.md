---
title: "leetcode-模拟119"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 移除石头游戏

Alice 和 Bob 在玩一个游戏，他们俩轮流从一堆石头中移除石头，Alice 先进行操作。

Alice 在第一次操作中移除 恰好 10 个石头。

接下来的每次操作中，每位玩家移除的石头数 恰好 为另一位玩家上一次操作的石头数减 1 。

第一位没法进行操作的玩家输掉这个游戏。

给你一个正整数 n 表示一开始石头的数目，如果 Alice 赢下这个游戏，请你返回 true ，否则返回 false 。


```
impl Solution {
    /// 判断 Alice 是否能赢得石头游戏
    ///
    /// 游戏规则：
    /// - Alice 先手，第一次必须移除恰好 10 个石头
    /// - 之后每次移除的石头数 = 上一位玩家移除数 - 1
    /// - 无法操作的玩家输
    ///
    /// # 核心思路
    /// 模拟每一轮双方依次取石的过程。每轮取石数递减：
    /// Alice 取 10，Bob 取 9，Alice 取 8，Bob 取 7，...
    ///
    /// 若在某一轮中当前玩家无法取出要求的数量（剩余石头不足），则该玩家输。
    /// 因此只需判断最后无法操作的是谁。
    ///
    /// # 参数
    /// - `n`: 初始石头总数
    ///
    /// # 返回值
    /// - `true` 表示 Alice 赢，`false` 表示 Bob 赢
    pub fn can_alice_win(n: i32) -> bool {
        // 当前轮次要求取出的石头数，从 Alice 的第一次 10 开始
        let mut required = 10;
        // 剩余石头数
        let mut remaining = n;

        // 模拟每一轮：双方轮流取石，每次取石数递减 1
        while remaining >= required {
            remaining -= required;
            required -= 1;
        }

        // 判断是谁无法操作：
        // required 从 10 递减，每轮减 1。
        // 若进行了奇数轮（10->9->8... 减了奇数次），说明 Alice 刚取完，轮到 Bob 时不够了，Bob 输，Alice 赢。
        // 若进行了偶数轮，说明 Bob 刚取完，轮到 Alice 时不够了，Alice 输。
        // (10 - required) 即为进行的轮次数。
        let rounds_played = 10 - required;
        rounds_played % 2 == 1
    }
}
```
