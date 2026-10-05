---
title: "leetcode-贪心150"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 将数组变相同的最小代价

给你两个长度都为 n 的整数数组 arr 和 brr 以及一个整数 k 。你可以对 arr 执行以下操作任意次：

将 arr 分割成若干个 连续的 子数组，并将这些子数组按任意顺序重新排列。这个操作的代价为 k 。

选择 arr 中的任意一个元素，将它增加或者减少一个正整数 x 。这个操作的代价为 x 。

请你返回将 arr 变为 brr 的 最小 总代价。

子数组 是一个数组中一段连续 非空 的元素序列。


```
impl Solution {
    pub fn min_cost(mut a: Vec<i32>, mut b: Vec<i32>, k: i64) -> i64 {
        // 策略一：不重排，直接逐位调整，代价为各位置差绝对值之和
        let cost_no_shuffle: i64 = a
            .iter()
            .zip(b.iter())
            .map(|(&x, &y)| (x as i64 - y as i64).abs())
            .sum::<i64>();

        // 若直接调整已不超过一次重排的代价，直接返回（重排至少要花 k）
        if cost_no_shuffle <= k {
            return cost_no_shuffle;
        }

        // 策略二：先花 k 重排 arr，再逐位调整。
        // 将 arr 与 brr 都升序排序后逐位匹配，能使差绝对值之和最小。
        a.sort_unstable();
        b.sort_unstable();
        let cost_shuffle: i64 = k
            + a.iter()
                .zip(b.iter())
                .map(|(&x, &y)| (x as i64 - y as i64).abs())
                .sum::<i64>();

        cost_no_shuffle.min(cost_shuffle)
    }
}
```
