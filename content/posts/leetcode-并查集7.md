---
title: "leetcode-并查集7"
date: 2026-06-25T11:22:21+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 岛屿的最大面积


给你一个大小为 m x n 的二进制矩阵 grid 。

岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在 水平或者竖直的四个方向上 相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。

岛屿的面积是岛上值为 1 的单元格的数目。

计算并返回 grid 中最大的岛屿面积。如果没有岛屿，则返回面积为 0 。

```
impl Solution {
    pub fn max_area_of_island(mut grid: Vec<Vec<i32>>) -> i32 {
        let rows = grid.len();
        let cols = grid[0].len();
        let mut max_area = 0;

        // 方向数组：上、下、左、右
        const DIRS: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        for i in 0..rows {
            for j in 0..cols {
                // 遇到陆地开始探索岛屿
                if grid[i][j] == 1 {
                    let mut area = 0;
                    let mut stack = vec![(i, j)];
                    grid[i][j] = 0; // 标记已访问

                    while let Some((x, y)) = stack.pop() {
                        area += 1;

                        // 探索四个方向
                        for &(dx, dy) in &DIRS {
                            let nx = x as isize + dx;
                            let ny = y as isize + dy;

                            // 边界检查且为陆地
                            if nx >= 0 && nx < rows as isize
                                && ny >= 0 && ny < cols as isize
                                && grid[nx as usize][ny as usize] == 1
                            {
                                stack.push((nx as usize, ny as usize));
                                grid[nx as usize][ny as usize] = 0; // 立即标记，避免重复入栈
                            }
                        }
                    }

                    max_area = max_area.max(area);
                }
            }
        }

        max_area
    }
}
```
