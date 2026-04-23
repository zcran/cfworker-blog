---
title: "leetcode-博弈7"
date: 2026-04-09T20:51:30+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 石子游戏 IV

Alice 和 Bob 两个人轮流玩一个游戏，Alice 先手。

一开始，有 n 个石子堆在一起。每个人轮流操作，正在操作的玩家可以从石子堆里拿走 任意 非零 平方数 个石子。

如果石子堆里没有石子了，则无法操作的玩家输掉游戏。

给你正整数 n ，且已知两个人都采取最优策略。如果 Alice 会赢得比赛，那么返回 True ，否则返回 False 。

```
impl Solution {
    pub fn winner_square_game(n: i32) -> bool {
        let n = n as usize;
        // 预先生成所有不超过 n 的完全平方数
        let squares: Vec<usize> = (1..)
            .map(|x| x * x)
            .take_while(|&s| s <= n)
            .collect();

        // dp[i] 表示剩余 i 个石子时当前先手是否必胜
        let mut dp = vec![false; n + 1];

        for i in 0..=n {
            if dp[i] {
                // 如果当前状态是必胜态，不需要处理（因为不可能从必胜态推出新的必败态）
                continue;
            }
            // 当前是必败态，则 i + 任意平方数 都是必胜态
            for &sq in &squares {
                let next = i + sq;
                if next > n {
                    break;
                }
                dp[next] = true;
            }
        }

        dp[n]
    }
}
```


优化点说明：

1.预计算平方数列表：避免内层循环每次计算 j*j，同时利用 take_while 自动限制范围。
2.动态范围：使用传入的 n 作为数组长度，而不是固定大数组。
3.逻辑清晰：外层循环只处理必败态，标记其后继为必胜态，符合游戏定义。
4.提前终止：平方数列表有序，一旦 next > n 就跳出内层循环。
