---
title: "leetcode-动态规划131"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 分隔长廊的方案数

在一个图书馆的走廊里，有一些座位和装饰植物排成一列。给你一个下标从 0 开始，长度为 n 的字符串 corridor ，它包含字母 'S' 和 'P' ，其中每个 'S' 表示一个座位，每个 'P' 表示一株植物。

在下标 0 的左边和下标 n - 1 的右边 已经 分别各放了一个屏风。你还需要额外放置一些屏风。每一个位置 i - 1 和 i 之间（1 <= i <= n - 1），至多能放一个屏风。

请你将走廊用屏风划分为若干段，且每一段内都 恰好有两个座位 ，而每一段内植物的数目没有要求。可能有多种划分方案，如果两个方案中有任何一个屏风的位置不同，那么它们被视为 不同 方案。

请你返回划分走廊的方案数。由于答案可能很大，请你返回它对 10^9 + 7 取余 的结果。如果没有任何方案，请返回 0 。


```
impl Solution {
    pub fn number_of_ways(corridor: String) -> i32 {
        const MOD: i64 = 1_000_000_007;

        let mut prev_seat = -1;      // 上一座位（或座位对结束位置）的下标
        let mut seat_count = 0;      // 当前累计座位数
        let mut ways: i64 = 1;       // 方案数

        for (i, ch) in corridor.bytes().enumerate() {
            if ch == b'S' {
                seat_count += 1;

                // 每遇到一对座位后的下一个座位，说明可以在它与上一座位之间放屏风
                // 可选屏风位置数 = 当前座位下标 - 上一座位下标
                if seat_count >= 3 && seat_count % 2 == 1 {
                    ways = ways * (i as i64 - prev_seat as i64) % MOD;
                }

                prev_seat = i as i32;
            }
        }

        // 座位数必须至少为 2 且为偶数，否则无法每段恰好两个座位
        if seat_count < 2 || seat_count % 2 != 0 {
            return 0;
        }

        ways as i32
    }
}
```
