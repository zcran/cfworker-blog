---
title: "leetcode-枚举18"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 判断单词是否能放入填字游戏内

给你一个 m x n 的矩阵 board ，它代表一个填字游戏 当前 的状态。填字游戏格子中包含小写英文字母（已填入的单词），表示 空 格的 ' ' 和表示 障碍 格子的 '#' 。

如果满足以下条件，那么我们可以 水平 （从左到右 或者 从右到左）或 竖直 （从上到下 或者 从下到上）填入一个单词：

· 该单词不占据任何 '#' 对应的格子。
· 每个字母对应的格子要么是 ' ' （空格）要么与 board 中已有字母 匹配 。
· 如果单词是 水平 放置的，那么该单词左边和右边 相邻 格子不能为 ' ' 或小写英文字母。
· 如果单词是 竖直 放置的，那么该单词上边和下边 相邻 格子不能为 ' ' 或小写英文字母。

给你一个字符串 word ，如果 word 可以被放入 board 中，请你返回 true ，否则请返回 false 。


```
impl Solution {
    /// 判断单词是否可以放入填字游戏棋盘。
    /// 单词可水平或垂直放置，可正放或倒放。
    pub fn place_word_in_crossword(board: Vec<Vec<char>>, word: String) -> bool {
        let word_chars: Vec<char> = word.chars().collect();
        let reversed: Vec<char> = word.chars().rev().collect();
        let rows = board.len();
        let cols = board[0].len();

        // 检查水平方向（从左到右）
        if Self::check_horizontal(&board, &word_chars, rows, cols)
            || Self::check_horizontal(&board, &reversed, rows, cols)
        {
            return true;
        }

        // 检查竖直方向（从上到下）
        if Self::check_vertical(&board, &word_chars, rows, cols)
            || Self::check_vertical(&board, &reversed, rows, cols)
        {
            return true;
        }

        false
    }

    /// 检查水平方向是否可放置单词
    fn check_horizontal(board: &Vec<Vec<char>>, word: &[char], rows: usize, cols: usize) -> bool {
        let len = word.len();
        for r in 0..rows {
            let mut c = 0;
            while c < cols {
                // 跳过障碍
                if board[r][c] == '#' {
                    c += 1;
                    continue;
                }

                // 找到连续非障碍段
                let start = c;
                while c < cols && board[r][c] != '#' {
                    c += 1;
                }
                let end = c;

                // 检查该段是否能放下单词
                if end - start >= len && Self::can_place_horizontal(board, r, start, end, word) {
                    return true;
                }
            }
        }
        false
    }

    /// 检查水平方向某个连续段能否放置单词
    fn can_place_horizontal(
        board: &Vec<Vec<char>>,
        row: usize,
        start: usize,
        end: usize,
        word: &[char],
    ) -> bool {
        let len = word.len();
        let cols = board[0].len();

        // 尝试所有可能的起始位置
        for left in start..=end - len {
            let right = left + len - 1;

            // 检查左边界：左侧相邻格子不能是空格或字母
            if left > 0 {
                let left_ch = board[row][left - 1];
                if left_ch != '#' {
                    continue;
                }
            }

            // 检查右边界：右侧相邻格子不能是空格或字母
            if right + 1 < cols {
                let right_ch = board[row][right + 1];
                if right_ch != '#' {
                    continue;
                }
            }

            // 检查单词是否匹配
            let mut valid = true;
            for i in 0..len {
                let ch = board[row][left + i];
                if ch != ' ' && ch != word[i] {
                    valid = false;
                    break;
                }
            }

            if valid {
                return true;
            }
        }

        false
    }

    /// 检查竖直方向是否可放置单词
    fn check_vertical(board: &Vec<Vec<char>>, word: &[char], rows: usize, cols: usize) -> bool {
        let len = word.len();
        for c in 0..cols {
            let mut r = 0;
            while r < rows {
                // 跳过障碍
                if board[r][c] == '#' {
                    r += 1;
                    continue;
                }

                // 找到连续非障碍段
                let start = r;
                while r < rows && board[r][c] != '#' {
                    r += 1;
                }
                let end = r;

                // 检查该段是否能放下单词
                if end - start >= len && Self::can_place_vertical(board, c, start, end, word) {
                    return true;
                }
            }
        }
        false
    }

    /// 检查竖直方向某个连续段能否放置单词
    fn can_place_vertical(
        board: &Vec<Vec<char>>,
        col: usize,
        start: usize,
        end: usize,
        word: &[char],
    ) -> bool {
        let len = word.len();
        let rows = board.len();

        // 尝试所有可能的起始位置
        for top in start..=end - len {
            let bottom = top + len - 1;

            // 检查上边界：上方相邻格子不能是空格或字母
            if top > 0 {
                let top_ch = board[top - 1][col];
                if top_ch != '#' {
                    continue;
                }
            }

            // 检查下边界：下方相邻格子不能是空格或字母
            if bottom + 1 < rows {
                let bottom_ch = board[bottom + 1][col];
                if bottom_ch != '#' {
                    continue;
                }
            }

            // 检查单词是否匹配
            let mut valid = true;
            for i in 0..len {
                let ch = board[top + i][col];
                if ch != ' ' && ch != word[i] {
                    valid = false;
                    break;
                }
            }

            if valid {
                return true;
            }
        }

        false
    }
}
```
