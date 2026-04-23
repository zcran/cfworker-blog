---
title: "leetcode-博弈15"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 求和游戏

Alice 和 Bob 玩一个游戏，两人轮流行动，Alice 先手 。

给你一个 偶数长度 的字符串 num ，每一个字符为数字字符或者 '?' 。每一次操作中，如果 num 中至少有一个 '?' ，那么玩家可以执行以下操作：

选择一个下标 i 满足 num[i] == '?' 。
将 num[i] 用 '0' 到 '9' 之间的一个数字字符替代。
当 num 中没有 '?' 时，游戏结束。

Bob 获胜的条件是 num 中前一半数字的和 等于 后一半数字的和。Alice 获胜的条件是前一半的和与后一半的和 不相等 。

比方说，游戏结束时 num = "243801" ，那么 Bob 获胜，因为 2+4+3 = 8+0+1 。如果游戏结束时 num = "243803" ，那么 Alice 获胜，因为 2+4+3 != 8+0+3 。
在 Alice 和 Bob 都采取 最优 策略的前提下，如果 Alice 获胜，请返回 true ，如果 Bob 获胜，请返回 false 。


```
// 题目：Sum Game (LeetCode 1927)
// 规则：Alice 和 Bob 轮流将 '?' 替换成 0-9 的数字，Alice 先手。
//       若最终左半数字和等于右半数字和，则 Bob 胜，否则 Alice 胜。
// 数学结论：
//   - 设左半有 cnt1 个 '?'，数字和为 sum1；右半有 cnt2 个 '?'，数字和为 sum2。
//   - 若总问号数为奇数，Alice 能破坏平衡 → 必胜。
//   - 否则，最终差值 = (9*(cnt1 - cnt2)/2) + (sum1 - sum2)，若该值 ≠ 0 则 Alice 胜。
impl Solution {
    pub fn sum_game(num: String) -> bool {
        let n = num.len();
        let half = n / 2;

        // 将字符串分为左右两半，分别计算数字和与问号个数
        // 使用函数式风格：通过迭代器和 fold 一次性计算
        let (sum1, cnt1) = num.chars().take(half).fold((0, 0), |(sum, cnt), ch| {
            if ch == '?' {
                (sum, cnt + 1)
            } else {
                (sum + (ch as i32 - '0' as i32), cnt)
            }
        });

        let (sum2, cnt2) = num.chars().skip(half).fold((0, 0), |(sum, cnt), ch| {
            if ch == '?' {
                (sum, cnt + 1)
            } else {
                (sum + (ch as i32 - '0' as i32), cnt)
            }
        });

        let total_questions = cnt1 + cnt2;
        // 若总问号数为奇数，Alice 必胜
        if total_questions % 2 == 1 {
            return true;
        }

        // 计算平衡差值：9 * (cnt1 - cnt2) / 2 + (sum1 - sum2)
        // 注意整数除法在 Rust 中向零取整，但数学上 (cnt1-cnt2) 必为偶数（因为总数偶数），所以结果准确
        let diff = 9 * (cnt1 - cnt2) / 2 + (sum1 - sum2);
        diff != 0
    }
}
```
