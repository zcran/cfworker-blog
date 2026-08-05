---
title: "leetcode-回溯12"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 单词搜索

给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。


```
impl Solution {
    pub fn exist(mut board: Vec<Vec<char>>, word: String) -> bool {
        let (m, n) = (board.len(), board[0].len());
        let word = word.as_bytes();

        // 剪枝1：单词长度超过格子总数
        if word.len() > m * n {
            return false;
        }

        // 剪枝2：统计字符频率，如果某字符不够则直接返回
        let mut cnt = [0; 128];
        for row in &board {
            for &c in row {
                cnt[c as usize] += 1;
            }
        }
        for &c in word {
            cnt[c as usize] -= 1;
            if cnt[c as usize] < 0 {
                return false;
            }
        }

        // 剪枝3：从出现次数少的端点开始搜索，减少分支
        let first = word[0];
        let last = word[word.len() - 1];
        let (start_char, start_idx) = if cnt[first as usize] > cnt[last as usize] {
            (last, word.len() - 1)
        } else {
            (first, 0)
        };

        // 从起点开始搜索
        for i in 0..m {
            for j in 0..n {
                if board[i][j] == start_char as char
                    && Self::dfs(&mut board, word, i, j, start_idx, start_idx == 0) {
                    return true;
                }
            }
        }
        false
    }

    /// 深度优先搜索
    /// - board: 字符网格（会临时修改标记访问）
    /// - word: 单词字节数组
    /// - i, j: 当前格子坐标
    /// - idx: 当前匹配到的单词索引
    /// - forward: true 表示正向匹配，false 表示反向匹配
    fn dfs(
        board: &mut Vec<Vec<char>>,
        word: &[u8],
        i: usize,
        j: usize,
        idx: usize,
        forward: bool,
    ) -> bool {
        // 边界检查
        if i >= board.len() || j >= board[0].len() {
            return false;
        }

        // 检查当前字符是否匹配
        if board[i][j] != word[idx] as char {
            return false;
        }

        // 已经匹配到最后一个字符
        if (forward && idx + 1 == word.len()) || (!forward && idx == 0) {
            return true;
        }

        // 标记已访问
        let temp = board[i][j];
        board[i][j] = '\0';

        // 四个方向探索
        let next_idx = if forward { idx + 1 } else { idx - 1 };
        let directions = [(0, 1), (0, -1), (1, 0), (-1, 0)];
        for (dx, dy) in directions {
            let (ni, nj) = (i.wrapping_add(dx as usize), j.wrapping_add(dy as usize));
            if Self::dfs(board, word, ni, nj, next_idx, forward) {
                return true;
            }
        }

        // 回溯
        board[i][j] = temp;
        false
    }
}
```
