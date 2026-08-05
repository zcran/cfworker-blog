---
title: "leetcode-枚举65"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 放三个车的价值之和最大 I

给你一个 m x n 的二维整数数组 board ，它表示一个国际象棋棋盘，其中 board[i][j] 表示格子 (i, j) 的 价值 。

处于 同一行 或者 同一列 车会互相 攻击 。你需要在棋盘上放三个车，确保它们两两之间都 无法互相攻击 。

请你返回满足上述条件下，三个车所在格子 值 之和 最大 为多少。


```
impl Solution {
    pub fn maximum_value_sum(board: Vec<Vec<i32>>) -> i64 {
        let n = board.len();
        let m = board[0].len();

        // 预计算每行值最大的三列（列索引，值）
        // 使用固定大小数组，避免Vec动态分配
        let mut best = [[(0, i32::MIN); 3]; 200]; // 根据题目约束，最大行数200
        for i in 0..n {
            // 初始化为最小值
            best[i] = [(0, i32::MIN); 3];
            for j in 0..m {
                let val = board[i][j];
                // 插入排序：维护前三大的列
                for k in 0..3 {
                    if val > best[i][k].1 {
                        // 后移
                        for kk in (k + 1..3).rev() {
                            best[i][kk] = best[i][kk - 1];
                        }
                        best[i][k] = (j, val);
                        break;
                    }
                }
            }
        }

        let mut ans = i64::MIN;

        // 枚举三行，每行从预计算的前三列中选择
        // 由于每行只需要前3列，可以大幅减少搜索空间
        for i1 in 0..n {
            for k1 in 0..3 {
                let (col1, val1) = best[i1][k1];
                for i2 in i1 + 1..n {
                    for k2 in 0..3 {
                        let (col2, val2) = best[i2][k2];
                        if col1 == col2 {
                            continue;
                        }
                        for i3 in i2 + 1..n {
                            for k3 in 0..3 {
                                let (col3, val3) = best[i3][k3];
                                if col1 != col3 && col2 != col3 {
                                    let sum = val1 as i64 + val2 as i64 + val3 as i64;
                                    if sum > ans {
                                        ans = sum;
                                    }
                                    break; // 列值递减，第一个满足条件的已是该行最大值
                                }
                            }
                        }
                    }
                }
            }
        }

        ans
    }
}
```
