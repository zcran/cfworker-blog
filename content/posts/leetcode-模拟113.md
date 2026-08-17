---
title: "leetcode-模拟113"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 矩阵中的蛇

大小为 n x n 的矩阵 grid 中有一条蛇。蛇可以朝 四个可能的方向 移动。矩阵中的每个单元格都使用位置进行标识： grid[i][j] = (i * n) + j。

蛇从单元格 0 开始，并遵循一系列命令移动。

给你一个整数 n 表示 grid 的大小，另给你一个字符串数组 commands，其中包括 "UP"、"RIGHT"、"DOWN" 和 "LEFT"。题目测评数据保证蛇在整个移动过程中将始终位于 grid 边界内。

返回执行 commands 后蛇所停留的最终单元格的位置。


```
impl Solution {
    pub fn final_position_of_snake(n: i32, commands: Vec<String>) -> i32 {
        // 当前位置，从 0 开始 (对应矩阵左上角 grid[0][0])
        let mut position = 0;

        for command in commands {
            // 根据命令首字符判断移动方向
            match command.chars().next().unwrap() {
                'U' => position -= n,  // 向上移动: 行号减 1
                'D' => position += n,  // 向下移动: 行号加 1
                'L' => position -= 1,  // 向左移动: 列号减 1
                _   => position += 1,  // 向右移动: 列号加 1 (命令首字符为 'R')
            }
        }

        position
    }
}
```
