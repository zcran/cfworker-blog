---
title: "leetcode-前缀和52"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 网格游戏

给你一个下标从 0 开始的二维数组 grid ，数组大小为 2 x n ，其中 grid[r][c] 表示矩阵中 (r, c) 位置上的点数。现在有两个机器人正在矩阵上参与一场游戏。

两个机器人初始位置都是 (0, 0) ，目标位置是 (1, n-1) 。每个机器人只会 向右 ((r, c) 到 (r, c + 1)) 或 向下 ((r, c) 到 (r + 1, c)) 。

游戏开始，第一个 机器人从 (0, 0) 移动到 (1, n-1) ，并收集路径上单元格的全部点数。对于路径上所有单元格 (r, c) ，途经后 grid[r][c] 会重置为 0 。然后，第二个 机器人从 (0, 0) 移动到 (1, n-1) ，同样收集路径上单元的全部点数。注意，它们的路径可能会存在相交的部分。

第一个 机器人想要打击竞争对手，使 第二个 机器人收集到的点数 最小化 。与此相对，第二个 机器人想要 最大化 自己收集到的点数。两个机器人都发挥出自己的 最佳水平 的前提下，返回 第二个 机器人收集到的 点数 。


```
impl Solution {
    pub fn grid_game(grid: Vec<Vec<i32>>) -> i64 {
        let n = grid[0].len();

        // 第一个机器人必须从(0,0)走到(1,n-1)，总共只有两种路径模式：
        // 1. 在第一行走若干步后向下，然后一直走到终点
        // 2. 向下时机决定了上下两行各自被收集的点数

        // 计算第一行除第一个位置外的总点数（第二个机器人最多能从第一行收集的）
        let mut top_remaining: i64 = grid[0].iter().skip(1).map(|&x| x as i64).sum();
        let mut bottom_collected: i64 = 0; // 第二行已经收集的点数

        // 第二个机器人能收集到的最大值，等于第一个机器人未收集的最大值
        // 初始情况：第一个机器人在第一列就向下（收集grid[0][0]和grid[1][0]）
        let mut min_max_score = top_remaining; // 第二个机器人最多收集 top_remaining

        // 遍历每个可能的向下转折点（在第一行第 i 列转向第二行）
        for i in 1..n {
            // 第一个机器人在第 i 列向下：
            // - 第一行：收集 grid[0][0..=i]，剩余 top_remaining -= grid[0][i]
            top_remaining -= grid[0][i] as i64;

            // - 第二行：收集 grid[1][i..n-1]，当前已收集到 grid[1][i-1]
            bottom_collected += grid[1][i - 1] as i64;

            // 第二个机器人会选择路径，使得收集点数最大：
            // 它可以从第一行收集 top_remaining，或从第二行收集 bottom_collected
            // 取两者最大值，因为机器人会选择对自己最有利的路径
            let second_robot_score = top_remaining.max(bottom_collected);

            // 第一个机器人要最小化第二个机器人的得分，取所有转折点中的最小值
            min_max_score = min_max_score.min(second_robot_score);
        }

        min_max_score
    }
}
```
