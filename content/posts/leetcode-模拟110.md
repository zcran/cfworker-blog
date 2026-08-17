---
title: "leetcode-模拟110"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 找到连续赢 K 场比赛的第一位玩家

有 n 位玩家在进行比赛，玩家编号依次为 0 到 n - 1 。

给你一个长度为 n 的整数数组 skills 和一个 正 整数 k ，其中 skills[i] 是第 i 位玩家的技能等级。skills 中所有整数 互不相同 。

所有玩家从编号 0 到 n - 1 排成一列。

比赛进行方式如下：

队列中最前面两名玩家进行一场比赛，技能等级 更高 的玩家胜出。

比赛后，获胜者保持在队列的开头，而失败者排到队列的末尾。

这个比赛的赢家是 第一位连续 赢下 k 场比赛的玩家。

请你返回这个比赛的赢家编号。


```
impl Solution {
    pub fn find_winning_player(skills: Vec<i32>, k: i32) -> i32 {
        let n = skills.len();
        let k = k.min((n - 1) as i32) as usize;
        let mut winner = 0;
        let mut streak = 0;
        for j in 1..n {
            if skills[winner] < skills[j] {
                winner = j;
                streak = 1;
            } else {
                streak += 1;
            }
            if streak == k {
                break;
            }
        }
        winner as i32
    }
}
```
