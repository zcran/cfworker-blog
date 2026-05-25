---
title: "leetcode-拓扑排序4"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 矩阵中的最长递增路径

给定一个 m x n 整数矩阵 matrix ，找出其中 最长递增路径 的长度。

对于每个单元格，你可以往上，下，左，右四个方向移动。 你 不能 在 对角线 方向上移动或移动到 边界外（即不允许环绕）。

```
use std::collections::VecDeque;

impl Solution {
    /// 计算矩阵中最长递增路径的长度（LeetCode 329）。
    ///
    /// 算法思路：拓扑排序（BFS 层序遍历）
    /// 1. 定义“入度”为比当前单元格小的邻居个数。入度为 0 的点是当前局部最小值。
    /// 2. 将所有入度为 0 的单元格入队，作为路径的第一层。
    /// 3. 逐层处理队列中的单元格：对于每个出队单元格，检查其四个方向中值更大的邻居，
    ///    将这些邻居的入度减 1。若邻居入度变为 0，表示该邻居的所有更小前驱都已访问，
    ///    可以将其加入下一层队列。
    /// 4. 每处理完一层，路径长度加 1。最终层数即为最长递增路径的长度。
    pub fn longest_increasing_path(matrix: Vec<Vec<i32>>) -> i32 {
        // 边界：若矩阵为空，直接返回 0（题目保证至少有一个元素，此处仅作防御）
        if matrix.is_empty() || matrix[0].is_empty() {
            return 0;
        }

        let rows = matrix.len();
        let cols = matrix[0].len();

        // 方向数组：上、下、左、右
        const DIRECTIONS: [(isize, isize); 4] = [(1, 0), (-1, 0), (0, 1), (0, -1)];

        // ---------- 步骤 1：计算每个单元格的入度 ----------
        // indegree[i][j] = 有多少相邻单元格的值 < matrix[i][j]
        let mut indegree = vec![vec![0; cols]; rows];

        for i in 0..rows {
            for j in 0..cols {
                let current_val = matrix[i][j];
                // 检查四个方向的邻居，统计比当前值小的邻居个数
                let smaller_neighbors = DIRECTIONS
                    .iter()
                    .filter_map(|&(dx, dy)| {
                        let ni = i as isize + dx;
                        let nj = j as isize + dy;
                        if ni >= 0 && ni < rows as isize && nj >= 0 && nj < cols as isize {
                            let ni = ni as usize;
                            let nj = nj as usize;
                            if matrix[ni][nj] < current_val {
                                Some(())
                            } else {
                                None
                            }
                        } else {
                            None
                        }
                    })
                    .count();
                indegree[i][j] = smaller_neighbors;
            }
        }

        // ---------- 步骤 2：初始化队列，将所有入度为 0 的单元格入队 ----------
        let mut queue = VecDeque::new();
        for i in 0..rows {
            for j in 0..cols {
                if indegree[i][j] == 0 {
                    queue.push_back((i, j));
                }
            }
        }

        // ---------- 步骤 3：BFS 层序遍历 ----------
        let mut max_path_len = 0;
        while !queue.is_empty() {
            // 当前层的大小（所有入度为 0 的单元格构成当前层）
            let level_size = queue.len();
            // 每处理一层，路径长度加 1
            max_path_len += 1;

            for _ in 0..level_size {
                let (i, j) = queue.pop_front().unwrap();
                let current_val = matrix[i][j];

                // 检查四个方向的值更大的邻居
                for &(dx, dy) in &DIRECTIONS {
                    let ni = i as isize + dx;
                    let nj = j as isize + dy;
                    if ni >= 0 && ni < rows as isize && nj >= 0 && nj < cols as isize {
                        let ni = ni as usize;
                        let nj = nj as usize;
                        if matrix[ni][nj] > current_val {
                            // 当前节点是邻居的一个更小前驱，减少其入度
                            indegree[ni][nj] -= 1;
                            // 若入度变为 0，说明所有比它小的前驱都已访问，可以作为下一层
                            if indegree[ni][nj] == 0 {
                                queue.push_back((ni, nj));
                            }
                        }
                    }
                }
            }
        }

        max_path_len
    }
}
```
