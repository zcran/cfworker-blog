---
title: "leetcode-模拟34"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 找出数组游戏的赢家

给你一个由 不同 整数组成的整数数组 arr 和一个整数 k 。

每回合游戏都在数组的前两个元素（即 arr[0] 和 arr[1] ）之间进行。比较 arr[0] 与 arr[1] 的大小，较大的整数将会取得这一回合的胜利并保留在位置 0 ，较小的整数移至数组的末尾。当一个整数赢得 k 个连续回合时，游戏结束，该整数就是比赛的 赢家 。

返回赢得比赛的整数。

题目数据 保证 游戏存在赢家。


```
impl Solution {
    pub fn get_winner(arr: Vec<i32>, k: i32) -> i32 {
        let mut winner = arr[0];
        let mut streak = 0;

        for &challenger in &arr[1..] {
            if winner > challenger {
                streak += 1;
            } else {
                winner = challenger;
                streak = 1;
            }
            // 当前 winner 已连续击败 streak 个对手，达到 k 即结束
            if streak == k {
                return winner;
            }
        }

        // 遍历完仍未达到 k，说明 winner 是数组最大值，后续无人能击败它
        winner
    }
}
```
