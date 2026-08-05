---
title: "leetcode-滑动窗口54"
date: 2026-07-18T11:02:32+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 必须拿起的最小连续卡牌数

给你一个整数数组 cards ，其中 cards[i] 表示第 i 张卡牌的 值 。如果两张卡牌的值相同，则认为这一对卡牌 匹配 。

返回你必须拿起的最小连续卡牌数，以使在拿起的卡牌中有一对匹配的卡牌。如果无法得到一对匹配的卡牌，返回 -1 。


```
use std::collections::HashMap;

impl Solution {
    pub fn minimum_card_pickup(cards: Vec<i32>) -> i32 {
        let mut last_pos = HashMap::new();
        let mut min_len = i32::MAX;

        for (pos, &card) in cards.iter().enumerate() {
            if let Some(&prev) = last_pos.get(&card) {
                // 当前卡牌与上次出现位置构成一对匹配，区间长度为 pos - prev + 1
                min_len = min_len.min((pos - prev + 1) as i32);
            }
            // 更新该卡牌的最新位置
            last_pos.insert(card, pos);
        }

        if min_len == i32::MAX { -1 } else { min_len }
    }
}
```
