---
title: "leetcode-广度优先搜索5"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 太平洋大西洋水流问题

有一个 m × n 的矩形岛屿，与 太平洋 和 大西洋 相邻。 “太平洋” 处于大陆的左边界和上边界，而 “大西洋” 处于大陆的右边界和下边界。

这个岛被分割成一个由若干方形单元格组成的网格。给定一个 m x n 的整数矩阵 heights ， heights[r][c] 表示坐标 (r, c) 上单元格 高于海平面的高度 。

岛上雨水较多，如果相邻单元格的高度 小于或等于 当前单元格的高度，雨水可以直接向北、南、东、西流向相邻单元格。水可以从海洋附近的任何单元格流入海洋。

返回网格坐标 result 的 2D 列表 ，其中 result[i] = [ri, ci] 表示雨水从单元格 (ri, ci) 流动 既可流向太平洋也可流向大西洋 。


```
impl Solution {
    pub fn pacific_atlantic(heights: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        const DIRS: [(isize, isize); 4] = [(0, -1), (0, 1), (-1, 0), (1, 0)];

        let m = heights.len();
        let n = heights[0].len();

        // 从海洋边界反向 DFS：只能走向高度 >= 当前格子的相邻格子
        fn dfs(
            i: usize,
            j: usize,
            vis: &mut [Vec<bool>],
            heights: &[Vec<i32>],
            dirs: &[(isize, isize); 4],
        ) {
            vis[i][j] = true;
            for &(dx, dy) in dirs {
                let x = i as isize + dx;
                let y = j as isize + dy;
                // 边界检查 + 未访问 + 高度不低于当前格子
                if x >= 0
                    && x < heights.len() as isize
                    && y >= 0
                    && y < heights[0].len() as isize
                {
                    let (x, y) = (x as usize, y as usize);
                    if !vis[x][y] && heights[x][y] >= heights[i][j] {
                        dfs(x, y, vis, heights, dirs);
                    }
                }
            }
        }

        let mut pacific = vec![vec![false; n]; m];
        let mut atlantic = vec![vec![false; n]; m];

        // 太平洋：上边界 + 左边界
        for j in 0..n {
            dfs(0, j, &mut pacific, &heights, &DIRS);
        }
        for i in 1..m {
            dfs(i, 0, &mut pacific, &heights, &DIRS);
        }

        // 大西洋：下边界 + 右边界
        for j in 0..n {
            dfs(m - 1, j, &mut atlantic, &heights, &DIRS);
        }
        for i in 0..m - 1 {
            dfs(i, n - 1, &mut atlantic, &heights, &DIRS);
        }

        // 两片海洋都能到达的格子即为答案
        let mut ans = Vec::new();
        for i in 0..m {
            for j in 0..n {
                if pacific[i][j] && atlantic[i][j] {
                    ans.push(vec![i as i32, j as i32]);
                }
            }
        }
        ans
    }
}
```
