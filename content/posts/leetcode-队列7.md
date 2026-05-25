---
title: "leetcode-队列7"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## Dota2 参议院


Dota2 的世界里有两个阵营：Radiant（天辉）和 Dire（夜魇）

Dota2 参议院由来自两派的参议员组成。现在参议院希望对一个 Dota2 游戏里的改变作出决定。他们以一个基于轮为过程的投票进行。在每一轮中，每一位参议员都可以行使两项权利中的 一 项：

· 剥夺一名参议员的权利：一名参议员可以使另一名参议员在本轮及所有后续轮次中失去所有权利。
· 宣布胜利：如果参议员发现有权利投票的参议员都是 同一个阵营的 ，他可以宣布胜利并决定在游戏中的有关变化。

给你一个字符串 senate 代表每个参议员的阵营。字母 'R' 和 'D'分别代表了 Radiant（天辉）和 Dire（夜魇）。然后，如果有 n 个参议员，给定字符串的大小将是 n。

以轮为基础的过程从给定顺序的第一个参议员开始到最后一个参议员结束。这一过程将持续到投票结束。所有失去权利的参议员将在过程中被跳过。

假设每一位参议员都足够聪明，会为自己的政党做出最好的策略，你需要预测哪一方最终会宣布胜利并在 Dota2 游戏中决定改变。输出应该是 "Radiant" 或 "Dire" 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn predict_party_victory(senate: String) -> String {
        let mut r_queue = VecDeque::new();
        let mut d_queue = VecDeque::new();
        let n = senate.len();

        // 初始化队列
        for (i, ch) in senate.chars().enumerate() {
            match ch {
                'R' => r_queue.push_back(i),
                'D' => d_queue.push_back(i),
                _ => unreachable!(),
            }
        }

        // 模拟投票过程
        while !r_queue.is_empty() && !d_queue.is_empty() {
            let r = r_queue.pop_front().unwrap();
            let d = d_queue.pop_front().unwrap();
            if r < d {
                // R 议员禁掉 D，R 进入下一轮（索引 + n 表示后面的位置）
                r_queue.push_back(r + n);
            } else {
                // D 议员禁掉 R，D 进入下一轮
                d_queue.push_back(d + n);
            }
        }

        if !r_queue.is_empty() {
            "Radiant".to_string()
        } else {
            "Dire".to_string()
        }
    }
}
```
