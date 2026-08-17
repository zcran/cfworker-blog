---
title: "leetcode-模拟59"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 执行所有后缀指令

现有一个 n x n 大小的网格，左上角单元格坐标 (0, 0) ，右下角单元格坐标 (n - 1, n - 1) 。给你整数 n 和一个整数数组 startPos ，其中 startPos = [startrow, startcol] 表示机器人最开始在坐标为 (startrow, startcol) 的单元格上。

另给你一个长度为 m 、下标从 0 开始的字符串 s ，其中 s[i] 是对机器人的第 i 条指令：'L'（向左移动），'R'（向右移动），'U'（向上移动）和 'D'（向下移动）。

机器人可以从 s 中的任一第 i 条指令开始执行。它将会逐条执行指令直到 s 的末尾，但在满足下述条件之一时，机器人将会停止：

下一条指令将会导致机器人移动到网格外。

没有指令可以执行。

返回一个长度为 m 的数组 answer ，其中 answer[i] 是机器人从第 i 条指令 开始 ，可以执行的 指令数目 。


```
impl Solution {
    /// 计算从每条指令开始，机器人能执行多少条指令不出界。
    /// n: 网格边长；start_pos: [row, col]；s: 指令串（L/R/U/D）。
    pub fn execute_instructions(n: i32, start_pos: Vec<i32>, s: String) -> Vec<i32> {
        let s = s.as_bytes();
        let m = s.len();
        let mut ans = vec![0i32; m];
        let (start_row, start_col) = (start_pos[0], start_pos[1]);

        for i in 0..m {
            let (mut row, mut col) = (start_row, start_col);
            let mut j = i;

            while j < m {
                match s[j] {
                    b'U' => row -= 1,
                    b'D' => row += 1,
                    b'L' => col -= 1,
                    _    => col += 1, // b'R'
                }
                if row == -1 || row == n || col == -1 || col == n {
                    break;
                }
                j += 1;
            }
            ans[i] = (j - i) as i32;
        }
        ans
    }
}
```
