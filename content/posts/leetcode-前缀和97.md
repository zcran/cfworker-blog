---
title: "leetcode-前缀和97"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 得到更多分数的最少关卡数目

给你一个长度为 n 的二进制数组 possible 。

Alice 和 Bob 正在玩一个有 n 个关卡的游戏，游戏中有一些关卡是 困难 模式，其他的关卡是 简单 模式。如果 possible[i] == 0 ，那么第 i 个关卡是 困难 模式，两个玩家 都不可能 通过。一个玩家通过一个简单模式的关卡可以获得 1 分，遇到困难模式的关卡将失去 1 分。

游戏的一开始，Alice 将从第 0 级开始 按顺序 完成一些关卡，然后 Bob 会完成剩下的所有关卡。

假设两名玩家都采取最优策略，目的是 最大化 自己的得分，Alice 想知道自己 最少 需要完成多少个关卡，才能获得比 Bob 更多的分数。

请你返回 Alice 获得比 Bob 更多的分数所需要完成的 最少 关卡数目，如果 无法 达成，那么返回 -1 。

注意，每个玩家都至少需要完成 1 个关卡。


```
impl Solution {
    pub fn minimum_levels(possible: Vec<i32>) -> i32 {
        let n = possible.len();

        // 总分：1的个数 - 0的个数 = 2*ones - n
        let total: i32 = possible.iter().map(|&x| if x == 1 { 1 } else { -1 }).sum();

        let mut alice_score = 0;
        // Alice至少完成1关，Bob至少完成1关，所以最多完成n-1关
        for i in 0..n - 1 {
            alice_score += if possible[i] == 1 { 1 } else { -1 };
            // Alice得分 > Bob得分 => alice_score > total - alice_score
            // => 2 * alice_score > total
            if alice_score * 2 > total {
                return (i + 1) as i32;
            }
        }

        -1
    }
}
```
