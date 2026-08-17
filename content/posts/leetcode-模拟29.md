---
title: "leetcode-模拟29"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 找出井字棋的获胜者

井字棋 是由两个玩家 A 和 B 在 3 x 3 的棋盘上进行的游戏。井字棋游戏的规则如下：

玩家轮流将棋子放在空方格 (' ') 上。
第一个玩家 A 总是用 'X' 作为棋子，而第二个玩家 B 总是用 'O' 作为棋子。
'X' 和 'O' 只能放在空方格中，而不能放在已经被占用的方格上。
只要有 3 个相同的（非空）棋子排成一条直线（行、列、对角线）时，游戏结束。
如果所有方块都放满棋子（不为空），游戏也会结束。
游戏结束后，棋子无法再进行任何移动。

给你一个数组 moves，其中 moves[i] = [rowi, coli] 表示第 i 次移动在 grid[rowi][coli]。如果游戏存在获胜者（A 或 B），就返回该游戏的获胜者；如果游戏以平局结束，则返回 "Draw"；如果仍会有行动（游戏未结束），则返回 "Pending"。

你可以假设 moves 都 有效（遵循 井字棋 规则），网格最初是空的，A 将先行动。


```
impl Solution {
    /// 判断井字棋结果
    /// count[0..2]: 第0/1/2行的X数量  count[3..5]: 第0/1/2列的X数量
    /// count[6]: 主对角线  count[7]: 副对角线
    /// A 走奇数步(0,2,4...)，B 走偶数步(1,3,5...)
    pub fn tictactoe(moves: Vec<Vec<i32>>) -> String {
        let mut count = [0; 8];

        // 只检查最后一步的落子方（只有最后一步的人可能获胜）
        let last_player = if moves.len() % 2 == 1 { 'A' } else { 'B' };
        let step_indices: Vec<usize> = if last_player == 'A' {
            (0..moves.len()).step_by(2).collect() // A 的步数: 0, 2, 4...
        } else {
            (1..moves.len()).step_by(2).collect() // B 的步数: 1, 3, 5...
        };

        for &i in &step_indices {
            let r = moves[i][0] as usize;
            let c = moves[i][1] as usize;

            count[r] += 1;      // 行
            count[c + 3] += 1;  // 列

            if r == c {
                count[6] += 1;  // 主对角线
            }
            if r + c == 2 {
                count[7] += 1;  // 副对角线
            }
        }

        // 检查最后落子方是否获胜
        if count.iter().any(|&x| x == 3) {
            return last_player.to_string();
        }

        // 未分胜负
        if moves.len() < 9 {
            "Pending".to_string()
        } else {
            "Draw".to_string()
        }
    }
}
```
