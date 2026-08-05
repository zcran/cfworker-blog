---
title: "leetcode-枚举66"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 放三个车的价值之和最大 II

给你一个 m x n 的二维整数数组 board ，它表示一个国际象棋棋盘，其中 board[i][j] 表示格子 (i, j) 的 价值 。

处于 同一行 或者 同一列 车会互相 攻击 。你需要在棋盘上放三个车，确保它们两两之间都 无法互相攻击 。

请你返回满足上述条件下，三个车所在格子 值 之和 最大 为多少。


```
impl Solution {
    pub fn maximum_value_sum(board: Vec<Vec<i32>>) -> i64 {
        let m = board.len();
        let n = board[0].len();

        // 存储前三大的 (值, 列)，按值降序，列互不相同
        type Top3 = [(i32, usize); 3];

        // 初始化 Top3 为无效值
        fn empty_top3() -> Top3 {
            [(i32::MIN, usize::MAX); 3]
        }

        // 将 (val, col) 插入到累积的 Top3 中，保持降序且列唯一
        fn insert_top3(top3: &mut Top3, val: i32, col: usize) {
            // 1. 如果该列已存在，仅当新值更大时更新并重新排序
            for k in 0..3 {
                if top3[k].1 == col {
                    if val > top3[k].0 {
                        top3[k].0 = val;
                        // 重新排序（降序）
                        top3.sort_by_key(|&(v, _)| -v);
                    }
                    return;
                }
            }

            // 2. 列不存在，尝试插入
            // 如果 top3 已满且 val 不大于最小值，则忽略
            if top3[2].1 != usize::MAX && val <= top3[2].0 {
                return;
            }

            // 插入并保持降序
            let mut inserted = false;
            for k in 0..3 {
                if val > top3[k].0 {
                    // 后移
                    for kk in (k + 1..3).rev() {
                        top3[kk] = top3[kk - 1];
                    }
                    top3[k] = (val, col);
                    inserted = true;
                    break;
                }
            }
            if !inserted {
                // 若所有值都 >= val，但有空位（初始状态），则填入
                for k in 0..3 {
                    if top3[k].1 == usize::MAX {
                        top3[k] = (val, col);
                        break;
                    }
                }
            }
        }

        // 后缀数组：suf[i] 表示从第 i 行到末尾的所有行中，前三大的不同列 (值, 列)
        let mut suffix = vec![empty_top3(); m];
        let mut cur = empty_top3();
        for i in (2..m).rev() {
            for j in 0..n {
                insert_top3(&mut cur, board[i][j], j);
            }
            suffix[i] = cur;
        }

        let mut ans = i64::MIN;
        let mut prefix = empty_top3();

        // 枚举中间行 i（第二个车所在行）
        for i in 1..m - 1 {
            // 将第 i-1 行加入前缀（累积 0..i-1 行的前三）
            for j in 0..n {
                insert_top3(&mut prefix, board[i - 1][j], j);
            }

            // 枚举中间行的列 j2
            for j2 in 0..n {
                // 前缀中选第一个车，后缀中选第三个车
                for &(x, j1) in &prefix {
                    if j1 == usize::MAX || j1 == j2 {
                        continue;
                    }
                    for &(z, j3) in &suffix[i + 1] {
                        if j3 == usize::MAX || j3 == j2 || j3 == j1 {
                            continue;
                        }
                        let sum = x as i64 + board[i][j2] as i64 + z as i64;
                        if sum > ans {
                            ans = sum;
                        }
                        break; // 后缀按值降序，第一个满足的即为该前缀下的最优
                    }
                }
            }
        }

        ans
    }
}
```
