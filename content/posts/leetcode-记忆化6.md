---
title: "leetcode-记忆化6"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 祖玛游戏


你正在参与祖玛游戏的一个变种。

在这个祖玛游戏变体中，桌面上有 一排 彩球，每个球的颜色可能是：红色 'R'、黄色 'Y'、蓝色 'B'、绿色 'G' 或白色 'W' 。你的手中也有一些彩球。

你的目标是 清空 桌面上所有的球。每一回合：

· 从你手上的彩球中选出 任意一颗 ，然后将其插入桌面上那一排球中：两球之间或这一排球的任一端。

· 接着，如果有出现 三个或者三个以上 且 颜色相同 的球相连的话，就把它们移除掉。

  · 如果这种移除操作同样导致出现三个或者三个以上且颜色相同的球相连，则可以继续移除这些球，直到不再满足移除条件。

· 如果桌面上所有球都被移除，则认为你赢得本场游戏。

· 重复这个过程，直到你赢了游戏或者手中没有更多的球。

给你一个字符串 board ，表示桌面上最开始的那排球。另给你一个字符串 hand ，表示手里的彩球。请你按上述操作步骤移除掉桌上所有球，计算并返回所需的 最少 球数。如果不能移除桌上所有的球，返回 -1 。


```
use std::collections::HashMap;

impl Solution {
    /// Zuma 游戏：求清空桌面所需的最少插入次数。
    ///
    /// 算法：DFS + 记忆化搜索。
    /// - `board`: 当前桌面的球序列
    /// - `hand`: 手中球的序列（颜色'A'..'Z'）
    /// 返回值：最少插入次数，若无法清空则返回 -1。
    pub fn find_min_step(board: String, hand: String) -> i32 {
        // 统计手牌中每种颜色的数量，共 26 种字母
        let mut hand_counts = vec![0; 26];
        for c in hand.bytes() {
            hand_counts[(c - b'A') as usize] += 1;
        }

        let mut cache = HashMap::new();
        Self::dfs(board, &mut hand_counts, &mut cache)
    }

    /// 深度优先搜索，返回当前局面下清空桌面的最少步数，若不可达则返回 -1。
    ///
    /// # 参数
    /// - `board`: 当前桌面的字符串
    /// - `hand_counts`: 剩余手牌的颜色计数（可变引用，用于回溯）
    /// - `cache`: 记忆化缓存，键为“(board, 手牌序列化字符串)”
    fn dfs(
        board: String,
        hand_counts: &mut Vec<usize>,
        cache: &mut HashMap<String, i32>,
    ) -> i32 {
        // 生成缓存键：板子状态 + 手牌状态（序列化）
        let key = format!("{}#{}", board, Self::serialize(hand_counts));
        if let Some(&cached) = cache.get(&key) {
            return cached;
        }

        let mut min_steps = i32::MAX; // 初始化为最大值，表示不可达

        if board.is_empty() {
            // 桌面已空，无需任何操作
            min_steps = 0;
        } else {
            let board_bytes = board.as_bytes();

            // 尝试在 board 的每个位置插入手中每种颜色的球
            for pos in 0..board.len() {
                for color_idx in 0..hand_counts.len() {
                    // 判断该位置插入该颜色是否可能值得尝试
                    // 原始逻辑中的 worth_trying 条件：
                    // 1) 插入的颜色与当前字符相同 → 肯定尝试
                    // 2) 如果当前字符与前一个字符相同，且插入的颜色不同于当前字符，也尝试
                    //    （这种情况可能将两个相同字符隔开，但后续可能因插入而形成三个连续？实际原代码中此处允许了）
                    let worth_trying = if (board_bytes[pos] - b'A') as usize == color_idx {
                        true // 颜色匹配当前字符
                    } else if pos > 0 && board_bytes[pos] == board_bytes[pos - 1]
                        && (board_bytes[pos] - b'A') as usize != color_idx
                    {
                        true // 当前字符与前一个相同，且插入颜色不同，原逻辑仍尝试
                    } else {
                        false
                    };

                    if hand_counts[color_idx] > 0 && worth_trying {
                        // 使用该颜色的球
                        hand_counts[color_idx] -= 1;

                        // 构造插入后的新桌面
                        let mut new_board = board.clone();
                        new_board.insert(pos, (color_idx as u8 + b'A') as char);
                        let collapsed_board = Self::update_board(&new_board); // 消除连锁反应

                        let steps = Self::dfs(collapsed_board, hand_counts, cache);
                        if steps != -1 {
                            // 当前插入算一步，加上后续所需步数
                            min_steps = min_steps.min(steps + 1);
                        }

                        // 回溯，恢复手牌数量
                        hand_counts[color_idx] += 1;
                    }
                }
            }
        }

        // 如果 min_steps 仍是最大值，表示所有尝试都不可行
        if min_steps == i32::MAX {
            min_steps = -1;
        }

        cache.insert(key, min_steps);
        min_steps
    }

    /// 对桌面字符串执行连锁消除：反复删除长度 ≥3 的连续相同颜色块，直到稳定。
    /// 返回消除完成后的字符串。
    fn update_board(board: &str) -> String {
        let board_bytes = board.as_bytes();
        let len = board.len();
        let mut start = 0;

        while start < len {
            let mut end = start + 1;
            // 寻找从 start 开始的连续相同颜色段
            while end < len && board_bytes[end] == board_bytes[start] {
                end += 1;
            }
            // 若连续段长度 >= 3，则消除并递归处理剩下的部分
            if end - start >= 3 {
                let new_board = format!("{}{}", &board[..start], &board[end..]);
                return Self::update_board(&new_board);
            } else {
                start = end; // 继续扫描下一段
            }
        }
        board.to_string() // 无消除，返回原串
    }

    /// 将手牌计数序列化为字符串，用于缓存键。
    /// 例如：有 2 个 'A' 和 1 个 'B' 会生成 "A2B1"。
    fn serialize(hand_counts: &Vec<usize>) -> String {
        let mut key = String::new();
        for (idx, &count) in hand_counts.iter().enumerate() {
            if count > 0 {
                key.push((idx as u8 + b'A') as char);
                key.push_str(&count.to_string());
            }
        }
        key
    }
}
```
