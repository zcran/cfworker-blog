---
title: "leetcode-贪心24"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 一手顺子

Alice 手中有一把牌，她想要重新排列这些牌，分成若干组，使每一组的牌数都是 groupSize ，并且由 groupSize 张连续的牌组成。

给你一个整数数组 hand 其中 hand[i] 是写在第 i 张牌上的数值。如果她可能重新排列这些牌，返回 true ；否则，返回 false 。


```
use std::collections::BTreeMap;

impl Solution {
    pub fn is_n_straight_hand(hand: Vec<i32>, group_size: i32) -> bool {
        let n = hand.len();
        if n as i32 % group_size != 0 {
            return false;
        }

        // BTreeMap 按牌面值有序存储，便于每次从最小值开始构造顺子
        let mut count: BTreeMap<i32, i32> = BTreeMap::new();
        for h in hand {
            *count.entry(h).or_insert(0) += 1;
        }

        while let Some((&start, &c)) = count.iter().next() {
            // 以当前最小牌为起点，连续取 group_size 张构成一个顺子
            for x in start..start + group_size {
                match count.get_mut(&x) {
                    Some(v) => {
                        *v -= c;
                        if *v == 0 {
                            count.remove(&x);
                        }
                    }
                    None => return false,
                }
            }
        }

        true
    }
}
```
