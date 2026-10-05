---
title: "leetcode-广度优先搜索19"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 获取所有钥匙的最短路径

给定一个二维网格 grid ，其中：

'.' 代表一个空房间
'#' 代表一堵墙
'@' 是起点
小写字母代表钥匙
大写字母代表锁

我们从起点开始出发，一次移动是指向四个基本方向之一行走一个单位空间。我们不能在网格外面行走，也无法穿过一堵墙。如果途经一个钥匙，我们就把它捡起来。除非我们手里有对应的钥匙，否则无法通过锁。

假设 k 为 钥匙/锁 的个数，且满足 1 <= k <= 6，字母表中的前 k 个字母在网格中都有自己对应的一个小写和一个大写字母。换言之，每个锁有唯一对应的钥匙，每个钥匙也有唯一对应的锁。另外，代表钥匙和锁的字母互为大小写并按字母顺序排列。

返回获取所有钥匙所需要的移动的最少次数。如果无法获取所有钥匙，返回 -1 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn shortest_path_all_keys(grid: Vec<String>) -> i32 {
        let grid: Vec<Vec<u8>> = grid.iter().map(|s| s.bytes().collect()).collect();
        let (m, n) = (grid.len(), grid[0].len());

        // 起点位置 & 全部钥匙的位掩码
        let (mut sr, mut sc) = (0, 0);
        let mut all_keys = 0u8;
        for i in 0..m {
            for j in 0..n {
                match grid[i][j] {
                    b'@' => (sr, sc) = (i, j),
                    c if c.is_ascii_lowercase() => all_keys |= 1 << (c - b'a'),
                    _ => {}
                }
            }
        }

        // visited[i][j][keys]：在 (i,j) 持有 keys 掩码的状态是否已访问
        let mut visited = vec![vec![vec![false; 1 << 6]; n]; m];
        let mut queue = VecDeque::new();
        visited[sr][sc][0] = true;
        queue.push_back((sr, sc, 0u8, 0i32));

        const DIRS: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while let Some((r, c, keys, step)) = queue.pop_front() {
            // 集齐所有钥匙
            if keys == all_keys {
                return step;
            }

            for (dr, dc) in DIRS {
                let nr = r as isize + dr;
                let nc = c as isize + dc;
                if nr < 0 || nr >= m as isize || nc < 0 || nc >= n as isize {
                    continue;
                }
                let (nr, nc) = (nr as usize, nc as usize);
                let cell = grid[nr][nc];

                // 墙不能通行（这是之前漏掉的关键检查）
                if cell == b'#' {
                    continue;
                }

                // 踩到小写字母则拾取钥匙
                let new_keys = if cell.is_ascii_lowercase() {
                    keys | (1 << (cell - b'a'))
                } else {
                    keys
                };

                // 遇到锁且没有对应钥匙，无法通过
                if cell.is_ascii_uppercase() && keys >> (cell - b'A') & 1 == 0 {
                    continue;
                }

                // 该状态未访问则入队
                if !visited[nr][nc][new_keys as usize] {
                    visited[nr][nc][new_keys as usize] = true;
                    queue.push_back((nr, nc, new_keys, step + 1));
                }
            }
        }

        -1
    }
}
```
