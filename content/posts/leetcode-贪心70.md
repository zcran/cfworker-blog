---
title: "leetcode-贪心70"
date: 2026-10-03T12:55:38+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 你能构造出连续值的最大数目

给你一个长度为 n 的整数数组 coins ，它代表你拥有的 n 个硬币。第 i 个硬币的值为 coins[i] 。如果你从这些硬币中选出一部分硬币，它们的和为 x ，那么称，你可以 构造 出 x 。

请返回从 0 开始（包括 0 ），你最多能 构造 出多少个连续整数。

你可能有多个相同值的硬币。


```
impl Solution {
    pub fn get_maximum_consecutive(mut coins: Vec<i32>) -> i32 {
        coins.sort_unstable();

        // reach 表示当前能连续构造出 [0, reach) 的所有整数
        // 若下一枚硬币 x <= reach，则可把可达区间扩展到 reach + x
        // 否则出现断层，后续硬币更大，直接停止
        let mut reach = 1;
        for x in coins {
            if x > reach {
                break;
            }
            reach += x;
        }
        reach
    }
}
```
