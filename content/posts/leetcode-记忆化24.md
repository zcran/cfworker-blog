---
title: "leetcode-记忆化24"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 最佳运动员的比拼回合

n 名运动员参与一场锦标赛，所有运动员站成一排，并根据 最开始的 站位从 1 到 n 编号（运动员 1 是这一排中的第一个运动员，运动员 2 是第二个运动员，依此类推）。

锦标赛由多个回合组成（从回合 1 开始）。每一回合中，这一排从前往后数的第 i 名运动员需要与从后往前数的第 i 名运动员比拼，获胜者将会进入下一回合。如果当前回合中运动员数目为奇数，那么中间那位运动员将轮空晋级下一回合。

例如，当前回合中，运动员 1, 2, 4, 6, 7 站成一排
运动员 1 需要和运动员 7 比拼
运动员 2 需要和运动员 6 比拼
运动员 4 轮空晋级下一回合
每回合结束后，获胜者将会基于最开始分配给他们的原始顺序（升序）重新排成一排。

编号为 firstPlayer 和 secondPlayer 的运动员是本场锦标赛中的最佳运动员。在他们开始比拼之前，完全可以战胜任何其他运动员。而任意两个其他运动员进行比拼时，其中任意一个都有获胜的可能，因此你可以 裁定 谁是这一回合的获胜者。

给你三个整数 n、firstPlayer 和 secondPlayer 。返回一个由两个值组成的整数数组，分别表示两位最佳运动员在本场锦标赛中比拼的 最早 回合数和 最晚 回合数。

```
use std::collections::HashMap;

impl Solution {
    /// 返回两名指定选手可能的最早轮次和最晚轮次（LeetCode 1900）
    /// 参数：
    /// - n: 总选手人数（1 ≤ n ≤ 28）
    /// - first_player: 第一位选手的编号（1‑based）
    /// - second_player: 第二位选手的编号（1‑based）
    /// 返回：[最早回合数, 最晚回合数]
    pub fn earliest_and_latest(n: i32, first_player: i32, second_player: i32) -> Vec<i32> {
        // 保证 first ≤ second，利用对称性
        let (f, s) = if first_player <= second_player {
            (first_player as usize, second_player as usize)
        } else {
            (second_player as usize, first_player as usize)
        };
        let n = n as usize;

        // 记忆化缓存：键 (n, f, s) → (最早, 最晚)
        let mut memo = HashMap::new();

        // 递归函数定义（闭包，以便捕获 memo）
        fn dp(
            memo: &mut HashMap<(usize, usize, usize), (i32, i32)>,
            n: usize,
            f: usize,
            s: usize,
        ) -> (i32, i32) {
            // 查缓存
            if let Some(&res) = memo.get(&(n, f, s)) {
                return res;
            }

            // 基础情形：只剩两人（或更少？原逻辑当 f+s == n+1 时返回 (1,1)）
            if f + s == n + 1 {
                return (1, 1);
            }

            // 对称性：若 f+s > n+1，则转化为对称位置
            if f + s > n + 1 {
                let res = dp(memo, n, n + 1 - s, n + 1 - f);
                memo.insert((n, f, s), res);
                return res;
            }

            let half = (n + 1) / 2; // 左半区的大小（包括中位数）
            let mut earliest = i32::MAX;
            let mut latest = i32::MIN;

            if s <= half {
                // 两名选手都在左半区或中位
                // i 枚举左侧被淘汰的人数（在 f 之前），j 枚举 f 与 s 之间被淘汰的人数
                for i in 0..f {
                    for j in 0..(s - f) {
                        // 递归子问题：下一轮人数为 half，选手位置变为 i+1 和 i+j+2（1‑based）
                        let (x, y) = dp(memo, half, i + 1, i + j + 2);
                        earliest = earliest.min(x);
                        latest = latest.max(y);
                    }
                }
            } else {
                // s 在右半区
                let s_prime = n + 1 - s; // s' 是 s 在对称中的位置
                // mid 是右半区选手在下一轮中的起始偏移（具体推导见原解法）
                let mid = (n - 2 * s_prime + 1) / 2;
                for i in 0..f {
                    for j in 0..(s_prime - f) {
                        let (x, y) = dp(memo, half, i + 1, i + j + mid + 2);
                        earliest = earliest.min(x);
                        latest = latest.max(y);
                    }
                }
            }

            // 当前轮次贡献 +1
            let res = (earliest + 1, latest + 1);
            memo.insert((n, f, s), res);
            res
        }

        let (earliest, latest) = dp(&mut memo, n, f, s);
        vec![earliest, latest]
    }
}
```
