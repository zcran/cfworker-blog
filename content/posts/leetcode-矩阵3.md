---
title: "leetcode-矩阵3"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 不同路径 II

给定一个 m x n 的整数数组 grid。一个机器人初始位于 左上角（即 grid[0][0]）。机器人尝试移动到 右下角（即 grid[m - 1][n - 1]）。机器人每次只能向下或者向右移动一步。

网格中的障碍物和空位置分别用 1 和 0 来表示。机器人的移动路径中不能包含 任何 有障碍物的方格。

返回机器人能够到达右下角的不同路径数量。

测试用例保证答案小于等于 2 * 10^9。


```
impl Solution {
    pub fn unique_paths_with_obstacles(obstacle_grid: Vec<Vec<i32>>) -> i32 {
        let n = obstacle_grid.len();
        let m = obstacle_grid[0].len();

        // 一维滚动数组：dp[j] 表示到达当前行第 j 列的路径数。
        // 初始 dp[0] = 1，但若起点有障碍则为 0。
        let mut dp = vec![0; m];
        dp[0] = if obstacle_grid[0][0] == 0 { 1 } else { 0 };

        for i in 0..n {
            for j in 0..m {
                if obstacle_grid[i][j] == 1 {
                    // 障碍格不可达，路径数清零
                    dp[j] = 0;
                } else if j > 0 {
                    // dp[j] 保留的是上一行同一列的值（从上方来的路径数）
                    // 加上 dp[j-1]（从左侧来的路径数）
                    // 若左侧是障碍，dp[j-1] 已在上一次迭代被置 0，无需额外判断
                    dp[j] += dp[j - 1];
                }
                // j == 0 且无障碍：dp[0] 保持不变（只能从上方来）
            }
        }

        dp[m - 1]
    }
}
```
