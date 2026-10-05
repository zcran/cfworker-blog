---
title: "leetcode-广度优先搜索45"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 清理教室的最少移动

给你一个 m x n 的网格图 classroom，其中一个学生志愿者负责清理散布在教室里的垃圾。网格图中的每个单元格是以下字符之一：

'S' ：学生的起始位置
'L' ：必须收集的垃圾（收集后，该单元格变为空白）
'R' ：重置区域，可以将学生的能量恢复到最大值，无论学生当前的能量是多少（可以多次使用）
'X' ：学生无法通过的障碍物
'.' ：空白空间

同时给你一个整数 energy，表示学生的最大能量容量。学生从起始位置 'S' 开始，带着 energy 的能量出发。

每次移动到相邻的单元格（上、下、左或右）会消耗 1 单位能量。如果能量为 0，学生此时只有处在 'R' 格子时可以继续移动，此区域会将能量恢复到 最大 能量值 energy。

返回收集所有垃圾所需的 最少 移动次数，如果无法完成，返回 -1。




```
use std::collections::VecDeque;

impl Solution {
    pub fn min_moves(classroom: Vec<String>, energy: i32) -> i32 {
        let grid: Vec<Vec<u8>> = classroom.iter().map(|s| s.as_bytes().to_vec()).collect();
        let (m, n) = (grid.len(), grid[0].len());

        // 给每个垃圾 'L' 分配一个二进制位，用于状态压缩
        let mut start = (0usize, 0usize);
        let mut bit = vec![vec![0usize; n]; m];
        let mut litter_count = 0;
        for i in 0..m {
            for j in 0..n {
                match grid[i][j] {
                    b'S' => start = (i, j),
                    b'L' => {
                        bit[i][j] = 1 << litter_count;
                        litter_count += 1;
                    }
                    _ => {}
                }
            }
        }

        let full = (1usize << litter_count) - 1;

        // best_energy[i][j][mask]：到达 (i,j) 且已收集 mask 时，剩余能量的最大值
        // -1 表示尚未到达。保留“能量最大”的状态是最优的。
        let mut best = vec![vec![vec![-1i32; 1 << litter_count]; n]; m];
        best[start.0][start.1][0] = energy;

        // BFS 队列，逐层扩展
        let mut queue = VecDeque::new();
        queue.push_back((start.0, start.1, 0usize, energy, 0i32));

        const DIRS: [(isize, isize); 4] = [(0, 1), (0, -1), (1, 0), (-1, 0)];

        while let Some((x, y, mask, e, steps)) = queue.pop_front() {
            // 所有垃圾收集完毕
            if mask == full {
                return steps;
            }
            // 没能量且不在 R 上（已在扩展时保证），无法继续
            if e == 0 {
                continue;
            }

            for &(dx, dy) in &DIRS {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx < 0 || ny < 0 || nx >= m as isize || ny >= n as isize {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);

                let cell = grid[nx][ny];
                if cell == b'X' {
                    continue;
                }

                // R 恢复满能量，其余格子消耗 1
                let next_e = if cell == b'R' { energy } else { e - 1 };
                let next_mask = mask | bit[nx][ny];

                // 只有能量严格更多才入队（剪枝）
                if next_e > best[nx][ny][next_mask] {
                    best[nx][ny][next_mask] = next_e;
                    queue.push_back((nx, ny, next_mask, next_e, steps + 1));
                }
            }
        }

        -1
    }
}
```
