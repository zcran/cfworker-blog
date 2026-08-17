---
title: "leetcode-计数70"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 最好的扑克手牌

给你一个整数数组 ranks 和一个字符数组 suit 。你有 5 张扑克牌，第 i 张牌大小为 ranks[i] ，花色为 suits[i] 。

下述是从好到坏你可能持有的 手牌类型 ：

1."Flush"：同花，五张相同花色的扑克牌。
2."Three of a Kind"：三条，有 3 张大小相同的扑克牌。
3."Pair"：对子，两张大小一样的扑克牌。
4."High Card"：高牌，五张大小互不相同的扑克牌。

请你返回一个字符串，表示给定的 5 张牌中，你能组成的 最好手牌类型 。

注意：返回的字符串 大小写 需与题目描述相同。


```
impl Solution {
    pub fn best_hand(ranks: Vec<i32>, suits: Vec<char>) -> String {
        // 检查是否为同花（Flush）：所有花色相同
        if suits.iter().all(|&s| s == suits[0]) {
            return "Flush".to_string();
        }

        // 统计每个点数出现的次数（点数范围 1-13）
        let mut count = [0; 14];
        for &rank in &ranks {
            count[rank as usize] += 1;
        }

        // 找到最大出现次数，判断手牌类型
        match count.iter().max().unwrap() {
            3 | 4 | 5 => "Three of a Kind",  // 三条（3张以上相同，但这里只有5张牌）
            2 => "Pair",                      // 对子
            _ => "High Card",                 // 高牌（所有点数互不相同）
        }.to_string()
    }
}
```
