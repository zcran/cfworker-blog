---
title: "leetcode-博弈6"
date: 2026-04-09T20:51:30+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 石子游戏 III

Alice 和 Bob 继续他们的石子游戏。几堆石子 排成一行 ，每堆石子都对应一个得分，由数组 stoneValue 给出。

Alice 和 Bob 轮流取石子，Alice 总是先开始。在每个玩家的回合中，该玩家可以拿走剩下石子中的的前 1、2 或 3 堆石子 。比赛一直持续到所有石头都被拿走。

每个玩家的最终得分为他所拿到的每堆石子的对应得分之和。每个玩家的初始分数都是 0 。

比赛的目标是决出最高分，得分最高的选手将会赢得比赛，比赛也可能会出现平局。

假设 Alice 和 Bob 都采取 最优策略 。

如果 Alice 赢了就返回 "Alice" ，Bob 赢了就返回 "Bob"，分数相同返回 "Tie" 。



```
impl Solution {
    pub fn stone_game_iii(stone_value: Vec<i32>) -> String {
        let n = stone_value.len(); // 石子堆的数量
        let mut dp = vec![0; n + 3]; // dp[i] 表示从第 i 堆开始先手玩家能获得的最大分数，多出3个位置防止越界
        let mut suffix_sum = 0; // 后缀和，表示从当前索引到末尾的总价值

        // 从后向前遍历，因为 dp[i] 依赖 dp[i+1], dp[i+2], dp[i+3]
        for i in (0..n).rev() {
            suffix_sum += stone_value[i]; // 更新后缀和：累加当前堆的价值

            let mut best = i32::MIN; // 当前玩家能获得的最佳分数，初始化为最小整数

            // 枚举取 1、2 或 3 堆
            for take in 1..=3 {
                // 检查取 take 堆后是否越界（i+take <= n 表示索引合法）
                if i + take <= n {
                    // 当前玩家取 take 堆获得的分数 = 剩余总和 - 对手后续能获得的最优分数
                    let taken = suffix_sum - dp[i + take];
                    if taken > best {
                        best = taken; // 更新最佳分数
                    }
                }
            }
            dp[i] = best; // 存储从第 i 堆开始先手玩家的最优分数
        }

        let alice_score = dp[0]; // Alice 作为先手，最终得分为 dp[0]
        let bob_score = suffix_sum - alice_score; // Bob 得分为总分减去 Alice 得分

        // 比较得分，返回对应字符串
        match alice_score.cmp(&bob_score) {
            std::cmp::Ordering::Greater => "Alice".to_string(),
            std::cmp::Ordering::Less => "Bob".to_string(),
            std::cmp::Ordering::Equal => "Tie".to_string(),
        }
    }
}
```
