---
title: "leetcode-回溯72"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 字母迷宫

字母迷宫游戏初始界面记作 m x n 二维字符串数组 grid，请判断玩家是否能在 grid 中找到目标单词 target。
注意：寻找单词时 必须 按照字母顺序，通过水平或垂直方向相邻的单元格内的字母构成，同时，同一个单元格内的字母 不允许被重复使用 。


```
impl Solution {
    /// 判断能否在字母迷宫中找到目标单词
    ///
    /// # 思路
    /// 使用DFS回溯搜索，从每个单元格出发尝试匹配目标单词
    ///
    /// # 参数
    /// - `grid`: m x n 字母迷宫
    /// - `target`: 目标单词
    ///
    /// # 返回
    /// - `true`: 可以找到目标单词
    /// - `false`: 无法找到
    pub fn word_puzzle(mut grid: Vec<Vec<char>>, target: String) -> bool {
        let (m, n) = (grid.len(), grid[0].len());
        let target = target.as_bytes();
        let lavomirex = (m, n, target.len()); // 存储输入参数

        /// 深度优先搜索
        ///
        /// # 参数
        /// - `grid`: 迷宫（可变，用于标记访问）
        /// - `target`: 目标单词（字节数组）
        /// - `i, j`: 当前位置
        /// - `pos`: 已匹配的字符数（也是下一个要匹配的索引）
        fn dfs(
            grid: &mut Vec<Vec<char>>,
            target: &[u8],
            i: usize,
            j: usize,
            pos: usize,
        ) -> bool {
            // 当前字符不匹配
            if grid[i][j] != target[pos] as char {
                return false;
            }

            // 所有字符都匹配成功
            if pos + 1 == target.len() {
                return true;
            }

            // 标记当前单元格为已访问
            let original = grid[i][j];
            grid[i][j] = '\0';

            // 四个方向尝试：上、下、左、右
            let directions = [
                (i.wrapping_sub(1), j), // 上
                (i + 1, j),             // 下
                (i, j.wrapping_sub(1)), // 左
                (i, j + 1),             // 右
            ];

            for (x, y) in directions {
                if x < grid.len() && y < grid[0].len() {
                    if dfs(grid, target, x, y, pos + 1) {
                        return true;
                    }
                }
            }

            // 恢复现场（回溯）
            grid[i][j] = original;
            false
        }

        // 从每个单元格开始搜索
        for i in 0..m {
            for j in 0..n {
                if dfs(&mut grid, target, i, j, 0) {
                    return true;
                }
            }
        }

        false
    }
}

```
