---
title: "leetcode-博弈16"
date: 2026-04-09T20:51:31+08:00
tags: ["leetcode", "博弈"]
draft: false
---


## 如果相邻两个颜色均相同则删除当前颜色

总共有 n 个颜色片段排成一列，每个颜色片段要么是 'A' 要么是 'B' 。给你一个长度为 n 的字符串 colors ，其中 colors[i] 表示第 i 个颜色片段的颜色。

Alice 和 Bob 在玩一个游戏，他们 轮流 从这个字符串中删除颜色。Alice 先手 。

如果一个颜色片段为 'A' 且 相邻两个颜色 都是颜色 'A' ，那么 Alice 可以删除该颜色片段。Alice 不可以 删除任何颜色 'B' 片段。
如果一个颜色片段为 'B' 且 相邻两个颜色 都是颜色 'B' ，那么 Bob 可以删除该颜色片段。Bob 不可以 删除任何颜色 'A' 片段。
Alice 和 Bob 不能 从字符串两端删除颜色片段。
如果其中一人无法继续操作，则该玩家 输 掉游戏且另一玩家 获胜 。
假设 Alice 和 Bob 都采用最优策略，如果 Alice 获胜，请返回 true，否则 Bob 获胜，返回 false。

```
impl Solution {
    /// 判断 Alice（A）是否能在游戏中获胜（LeetCode 2038. 如果相邻两个颜色均相同则删除当前颜色）
    /// 游戏规则：每次可以选择一个颜色，如果它的左右相邻颜色相同（且至少存在两个连续），则删除它。
    /// 等价于：对于每个长度 >= 3 的连续段，可以删除的次数 = 长度 - 2。
    /// Alice 只能删除 'A' 的段，Bob 只能删除 'B' 的段。Alice 先手，无法行动者输。
    /// 所以只要 Alice 可删除的总次数 > Bob 可删除的总次数，Alice 就获胜。
    pub fn winner_of_game(colors: String) -> bool {
        // 使用迭代器 + fold 一次遍历统计 A 和 B 的额外删除次数
        // 状态 (alice_extra, bob_extra, current_achain, current_bchain)
        let (alice_extra, bob_extra, _, _) = colors.chars().fold(
            (0, 0, 0, 0),
            |(mut a_extra, mut b_extra, mut a_len, mut b_len), ch| match ch {
                'A' => {
                    // 连续 A 的长度加一
                    a_len += 1;
                    // 只有当连续长度 >= 3 时，每增加一个 A 就可以多一次删除机会
                    if a_len > 2 {
                        a_extra += 1;
                    }
                    // 遇到 A 时重置 B 的连续计数
                    b_len = 0;
                    (a_extra, b_extra, a_len, b_len)
                }
                'B' => {
                    b_len += 1;
                    if b_len > 2 {
                        b_extra += 1;
                    }
                    a_len = 0;
                    (a_extra, b_extra, a_len, b_len)
                }
                // 题目保证只有 'A' 和 'B'，但 match 需要覆盖所有情况
                _ => (a_extra, b_extra, a_len, b_len),
            },
        );
        // Alice 获胜当且仅当她可删除的次数多于 Bob
        alice_extra > bob_extra
    }
}
```
