---
title: "leetcode-模拟15"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 机器人能否返回原点

在二维平面上，有一个机器人从原点 (0, 0) 开始。给出它的移动顺序，判断这个机器人在完成移动后是否在 (0, 0) 处结束。

移动顺序由字符串 moves 表示。字符 move[i] 表示其第 i 次移动。机器人的有效动作有 R（右），L（左），U（上）和 D（下）。

如果机器人在完成所有动作后返回原点，则返回 true。否则，返回 false。

注意：机器人“面朝”的方向无关紧要。 “R” 将始终使机器人向右移动一次，“L” 将始终向左移动等。此外，假设每次移动机器人的移动幅度相同。


```
impl Solution {
    pub fn judge_circle(moves: String) -> bool {
        let (mut x, mut y) = (0, 0);
        for &b in moves.as_bytes() {
            match b {
                b'U' => y += 1,
                b'D' => y -= 1,
                b'L' => x -= 1,
                b'R' => x += 1,
                _ => {} // 题目保证只有 UDLR，此行仅为穷尽匹配
            }
        }
        x == 0 && y == 0
    }
}
```
