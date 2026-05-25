---
title: "leetcode-位掩码9"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 参加考试的最大学生数

给你一个 m * n 的矩阵 seats 表示教室中的座位分布。如果座位是坏的（不可用），就用 '#' 表示；否则，用 '.' 表示。

学生可以看到左侧、右侧、左上、右上这四个方向上紧邻他的学生的答卷，但是看不到直接坐在他前面或者后面的学生的答卷。请你计算并返回该考场可以容纳的同时参加考试且无法作弊的 最大 学生人数。

学生必须坐在状况良好的座位上。

```
impl Solution {
    pub fn max_students(seats: Vec<Vec<char>>) -> i32 {
        let m = seats.len();
        let n = seats[0].len();

        // 每排可用的椅子掩码（'.' 的位置为1）
        let mut row_mask = vec![0; m];
        for i in 0..m {
            let mut mask = 0;
            for j in 0..n {
                if seats[i][j] == '.' {
                    mask |= 1 << j;
                }
            }
            row_mask[i] = mask;
        }

        // 所有合法的行内状态（无相邻1且是可用椅子的子集）
        let all_states: Vec<usize> = (0..(1 << n))
            .filter(|&s| (s & (s >> 1)) == 0) // 无左右相邻
            .collect();

        // dp[state] = 处理完上一排后，上一排状态为 state 时的最大人数
        let mut dp = vec![0; 1 << n];
        for i in 0..m {
            let mut new_dp = vec![0; 1 << n];
            for &cur in &all_states {
                // 当前行状态必须包含于可用椅子
                if (cur & row_mask[i]) != cur {
                    continue;
                }
                let cnt = cur.count_ones() as i32;
                // 枚举上一行的合法状态
                for &prev in &all_states {
                    // 检查上下左右冲突：
                    // 1. 当前行状态不能与上一行状态在斜对角冲突
                    //    (cur & (prev << 1)) == 0 && (cur & (prev >> 1)) == 0
                    // 2. 当前行自己已经保证无左右相邻，上一行自己也是合法状态
                    if (cur & (prev << 1)) == 0 && (cur & (prev >> 1)) == 0 {
                        new_dp[cur] = new_dp[cur].max(dp[prev] + cnt);
                    }
                }
            }
            dp = new_dp;
        }

        *dp.iter().max().unwrap()
    }
}
```
