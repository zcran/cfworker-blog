---
title: "leetcode-并查集48"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 矩阵查询可获得的最大分数

给你一个大小为 m x n 的整数矩阵 grid 和一个大小为 k 的数组 queries 。

找出一个大小为 k 的数组 answer ，且满足对于每个整数 queries[i] ，你从矩阵 左上角 单元格开始，重复以下过程：

· 如果 queries[i] 严格 大于你当前所处位置单元格，如果该单元格是第一次访问，则获得 1 分，并且你可以移动到所有 4 个方向（上、下、左、右）上任一 相邻 单元格。
· 否则，你不能获得任何分，并且结束这一过程。

在过程结束后，answer[i] 是你可以获得的最大分数。注意，对于每个查询，你可以访问同一个单元格 多次 。

返回结果数组 answer 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn max_points(grid: Vec<Vec<i32>>, queries: Vec<i32>) -> Vec<i32> {
        let m = grid.len();
        let n = grid[0].len();

        // 将查询与索引绑定，按值升序排序
        let mut sorted_queries: Vec<(usize, i32)> = queries.into_iter().enumerate().collect();
        sorted_queries.sort_unstable_by_key(|&(_, val)| val);

        // 将所有单元格按值排序
        let mut cells: Vec<(i32, usize, usize)> = Vec::with_capacity(m * n);
        for i in 0..m {
            for j in 0..n {
                cells.push((grid[i][j], i, j));
            }
        }
        cells.sort_unstable_by_key(|&(val, _, _)| val);

        let mut ans = vec![0; sorted_queries.len()];
        let mut visited = vec![false; m * n];
        let mut score = 0;
        let mut cell_idx = 0;

        // 方向数组
        let dirs = [(0, 1), (0, -1), (1, 0), (-1, 0)];

        for (query_idx, query_val) in sorted_queries {
            // 将所有值小于当前查询的单元格标记为可访问
            while cell_idx < cells.len() && cells[cell_idx].0 < query_val {
                let (_, i, j) = cells[cell_idx];
                let pos = i * n + j;

                // 如果该单元格未被访问，尝试从已访问的邻居扩展
                if !visited[pos] {
                    // 检查上下左右是否有已访问的单元格
                    let mut can_reach = false;
                    if i == 0 && j == 0 {
                        can_reach = true; // 左上角始终可达
                    } else {
                        for (di, dj) in dirs {
                            let ni = i as isize + di;
                            let nj = j as isize + dj;
                            if ni >= 0 && ni < m as isize && nj >= 0 && nj < n as isize {
                                if visited[ni as usize * n + nj as usize] {
                                    can_reach = true;
                                    break;
                                }
                            }
                        }
                    }

                    if can_reach {
                        // BFS扩展所有连通的可访问单元格
                        let mut queue = VecDeque::new();
                        queue.push_back((i, j));
                        visited[pos] = true;
                        score += 1;

                        while let Some((ci, cj)) = queue.pop_front() {
                            for (di, dj) in dirs {
                                let ni = ci as isize + di;
                                let nj = cj as isize + dj;
                                if ni >= 0 && ni < m as isize && nj >= 0 && nj < n as isize {
                                    let npos = ni as usize * n + nj as usize;
                                    if !visited[npos] && grid[ni as usize][nj as usize] < query_val {
                                        visited[npos] = true;
                                        score += 1;
                                        queue.push_back((ni as usize, nj as usize));
                                    }
                                }
                            }
                        }
                    }
                }
                cell_idx += 1;
            }

            ans[query_idx] = score;
        }

        ans
    }
}
```
