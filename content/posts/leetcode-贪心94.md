---
title: "leetcode-贪心94"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 得到目标值的最少行动次数

你正在玩一个整数游戏。从整数 1 开始，期望得到整数 target 。

在一次行动中，你可以做下述两种操作之一：

递增，将当前整数的值加 1（即， x = x + 1）。
加倍，使当前整数的值翻倍（即，x = 2 * x）。

在整个游戏过程中，你可以使用 递增 操作 任意 次数。但是只能使用 加倍 操作 至多 maxDoubles 次。

给你两个整数 target 和 maxDoubles ，返回从 1 开始得到 target 需要的最少行动次数。


```
impl Solution {
    pub fn min_moves(target: i32, max_doubles: i32) -> i32 {
        // 逆向思考：从 target 回到 1。
        // 加倍操作对应逆向的除以 2，递增对应逆向的减 1。
        // 贪心：能用加倍时，若 target 为偶数就除以 2；
        // 若为奇数则先减 1 变成偶数，再除以 2。
        // 加倍次数用尽后，只能一路递减到 1。
        let mut target = target;
        let mut doubles = max_doubles;
        let mut moves = 0;

        while target > 1 {
            if doubles == 0 {
                // 没有加倍可用，剩下的只能靠递增（逆向减 1）
                moves += target - 1;
                break;
            }
            if target & 1 == 1 {
                // 奇数：先减 1 变成偶数
                moves += 1;
                target -= 1;
            } else {
                // 偶数：除以 2
                doubles -= 1;
                moves += 1;
                target >>= 1;
            }
        }
        moves
    }
}
```
