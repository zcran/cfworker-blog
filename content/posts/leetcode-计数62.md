---
title: "leetcode-计数62"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 找出输掉零场或一场比赛的玩家

给你一个整数数组 matches 其中 matches[i] = [winneri, loseri] 表示在一场比赛中 winneri 击败了 loseri 。

返回一个长度为 2 的列表 answer ：

· answer[0] 是所有 没有 输掉任何比赛的玩家列表。
· answer[1] 是所有恰好输掉 一场 比赛的玩家列表。

两个列表中的值都应该按 递增 顺序返回。

注意：

· 只考虑那些参与 至少一场 比赛的玩家。
· 生成的测试用例保证 不存在 两场比赛结果 相同 。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_winners(matches: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        // 统计每个玩家的失败次数
        let mut losses = HashMap::with_capacity(matches.len() * 2);

        for m in matches {
            let winner = m[0];
            let loser = m[1];

            // 胜者确保在表中，但失败次数不变
            losses.entry(winner).or_insert(0);
            // 败者失败次数 +1
            *losses.entry(loser).or_insert(0) += 1;
        }

        // 按失败次数分组：0次和1次
        let mut no_loss = Vec::new();
        let mut one_loss = Vec::new();

        for (player, loss_count) in losses {
            match loss_count {
                0 => no_loss.push(player),
                1 => one_loss.push(player),
                _ => {}
            }
        }

        // 按递增顺序排序
        no_loss.sort_unstable();
        one_loss.sort_unstable();

        vec![no_loss, one_loss]
    }
}
```
