---
title: "leetcode-二分查找39"
date: 2026-09-28T12:29:12+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 两球之间的磁力

在代号为 C-137 的地球上，Rick 发现如果他将两个球放在他新发明的篮子里，它们之间会形成特殊形式的磁力。Rick 有 n 个空的篮子，第 i 个篮子的位置在 position[i] ，Morty 想把 m 个球放到这些篮子里，使得任意两球间 最小磁力 最大。

已知两个球如果分别位于 x 和 y ，那么它们之间的磁力为 |x - y| 。

给你一个整数数组 position 和一个整数 m ，请你返回最大化的最小磁力。


```
impl Solution {
    /// 返回放置 m 个球后，任意两球间最小磁力的最大值。
    ///
    /// 思路：二分答案。
    ///   将 position 排序后，最小距离 d 越大越难满足，具备单调性。
    ///   对给定最小距离 d，贪心放置：从第一个篮子开始，
    ///   每次选距离上一个已放球 >= d 的最近篮子，统计能放多少个球。
    ///   若 >= m 则 d 可行，尝试更大；否则需更小。
    ///
    ///   下界 lo = 1，上界 hi = position[last] - position[0]。
    pub fn max_distance(position: Vec<i32>, m: i32) -> i32 {
        let mut position = position;
        position.sort_unstable();

        // 判断最小距离 d 能否放下 m 个球
        let feasible = |d: i32| -> bool {
            let mut count = 1;
            let mut last = position[0];
            for &pos in &position[1..] {
                if pos - last >= d {
                    last = pos;
                    count += 1;
                    if count >= m {
                        return true;
                    }
                }
            }
            count >= m
        };

        // 二分查找最大可行距离
        let (mut lo, mut hi) = (1, position[position.len() - 1] - position[0]);
        while lo < hi {
            // 向上取整，避免死循环
            let mid = lo + (hi - lo + 1) / 2;
            if feasible(mid) {
                lo = mid;
            } else {
                hi = mid - 1;
            }
        }
        lo
    }
}
```
