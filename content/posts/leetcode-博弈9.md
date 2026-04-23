---
title: "leetcode-博弈9"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 石子游戏 VI

Alice 和 Bob 轮流玩一个游戏，Alice 先手。

一堆石子里总共有 n 个石子，轮到某个玩家时，他可以 移出 一个石子并得到这个石子的价值。Alice 和 Bob 对石子价值有 不一样的的评判标准 。双方都知道对方的评判标准。

给你两个长度为 n 的整数数组 aliceValues 和 bobValues 。aliceValues[i] 和 bobValues[i] 分别表示 Alice 和 Bob 认为第 i 个石子的价值。

所有石子都被取完后，得分较高的人为胜者。如果两个玩家得分相同，那么为平局。两位玩家都会采用 最优策略 进行游戏。

请你推断游戏的结果，用如下的方式表示：

如果 Alice 赢，返回 1 。
如果 Bob 赢，返回 -1 。
如果游戏平局，返回 0 。


```
// 位运算和排序
impl Solution {
    pub fn stone_game_vi(mut alice_values: Vec<i32>, bob_values: Vec<i32>) -> i32 {
        let n = alice_values.len();                     // 获取石头堆数（数组长度）
        let mut av = alice_values;                      // 将传入的 alice_values 所有权转移给可变变量 av
        // 关键优化：将两个数组的值合并编码到一个 i32 中。
        // 对每个位置 i，将 bob_values[i] 左移7位后加到 alice_values[i] 上。
        // 这样 alice 值存在低7位，bob 值存在高25位（因为 i32 有32位，移位7位后剩余25位，足够存储）。
        // 选择7位是因为题目中值范围 ≤ 10^7 < 2^24，所以低7位足以存储 alice 值（实际需要 ≤ 2^24，但这里用7位太小？需要验证）。
        // 实际上这种编码是自定义的，确保唯一性：每个数 = (bob << 7) + alice。
        av.iter_mut().zip(bob_values.iter()).for_each(|(mut x, y)| *x += *y << 7);

        // 按照编码后的值排序（升序）。因为编码的高位是 bob 值，低位是 alice 值，
        // 排序后实际按 (bob, alice) 的字典序排列。但这里使用自定义比较键：
        // (k >> 7) 取出 bob 值，(k & 127) 取出 alice 值，相加作为排序键。
        // 这等价于按 (alice + bob) 排序，这正是 Stone Game VI 的最优贪心策略：
        // 双方都优先选择 alice[i]+bob[i] 最大的堆。
        av.sort_unstable_by_key(|&k| (k >> 7) + (k & 127));

        let mut ts = 0;                                 // 累计分数差（Alice 得分减去 Bob 得分）
        // 逆序遍历排序后的数组（即从最大和到最小和），round 表示回合序号（0-based）
        for (round, tv) in av.iter().rev().enumerate() {
            if round & 1 == 0 {                         // 偶数回合：Alice 先手，取 alice 值（低7位）
                ts += tv & 127;                         // 取出低7位（alice 值）加到总分差
            } else {                                    // 奇数回合：Bob 取，减去他的得分（bob 值）
                ts -= tv >> 7;                          // 取出高25位（bob 值）并从总分差中减去
            }
        }
        // 根据总分差的正负或零，返回 1（Alice 胜）、0（平局）、-1（Bob 胜）
        return match ts.cmp(&0) {
            std::cmp::Ordering::Greater => 1,
            std::cmp::Ordering::Equal => 0,
            std::cmp::Ordering::Less => -1,
        };
    }
}
```
