---
title: "leetcode-模拟16"
date: 2026-08-08T11:31:09+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 隔离病毒

病毒扩散得很快，现在你的任务是尽可能地通过安装防火墙来隔离病毒。

假设世界由 m x n 的二维矩阵 isInfected 组成， isInfected[i][j] == 0 表示该区域未感染病毒，而  isInfected[i][j] == 1 表示该区域已感染病毒。可以在任意 2 个相邻单元之间的共享边界上安装一个防火墙（并且只有一个防火墙）。

每天晚上，病毒会从被感染区域向相邻未感染区域扩散，除非被防火墙隔离。现由于资源有限，每天你只能安装一系列防火墙来隔离其中一个被病毒感染的区域（一个区域或连续的一片区域），且该感染区域对未感染区域的威胁最大且 保证唯一 。

你需要努力使得最后有部分区域不被病毒感染，如果可以成功，那么返回需要使用的防火墙个数; 如果无法实现，则返回在世界被病毒全部感染时已安装的防火墙个数。


```
use std::collections::HashSet;

impl Solution {
    pub fn contain_virus(mut grid: Vec<Vec<i32>>) -> i32 {
        let (m, n) = (grid.len(), grid[0].len());
        let dirs = [(-1, 0), (1, 0), (0, -1), (0, 1)];
        let mut ans = 0;

        // 每天的模拟循环
        loop {
            let mut vis = vec![vec![false; n]; m];
            // 每个区域的信息: (感染细胞编码列表, 边界0格编码集合, 所需墙数)
            let mut regions: Vec<(Vec<usize>, HashSet<usize>, i32)> = Vec::new();

            // 找出所有感染区域
            for i in 0..m {
                for j in 0..n {
                    if grid[i][j] == 1 && !vis[i][j] {
                        let mut cells = Vec::new();
                        let mut frontier = HashSet::new();
                        let mut walls = 0;
                        Self::dfs(&grid, &mut vis, i, j, m, n, &dirs,
                                  &mut cells, &mut frontier, &mut walls);
                        if !frontier.is_empty() {
                            regions.push((cells, frontier, walls));
                        }
                    }
                }
            }

            // 没有可扩散的感染区域，结束
            if regions.is_empty() {
                break;
            }

            // 找出威胁最大的区域（边界0格最多的）
            let mut max_idx = 0;
            for i in 1..regions.len() {
                if regions[i].1.len() > regions[max_idx].1.len() {
                    max_idx = i;
                }
            }

            // 对该区域建墙隔离
            ans += regions[max_idx].2;
            for &code in &regions[max_idx].0 {
                let (i, j) = (code / n, code % n);
                grid[i][j] = -1; // 标记为已隔离
            }

            // 其他区域的病毒扩散
            for (idx, (_, frontier, _)) in regions.iter().enumerate() {
                if idx == max_idx { continue; }
                for &code in frontier {
                    let (i, j) = (code / n, code % n);
                    grid[i][j] = 1;
                }
            }
        }

        ans
    }

    /// DFS 探索一个感染区域，收集感染细胞、边界0格和所需墙数
    fn dfs(grid: &[Vec<i32>], vis: &mut [Vec<bool>], i: usize, j: usize,
           m: usize, n: usize, dirs: &[(i32, i32)],
           cells: &mut Vec<usize>, frontier: &mut HashSet<usize>, walls: &mut i32) {
        vis[i][j] = true;
        cells.push(i * n + j);

        for &(di, dj) in dirs {
            let ni = i as i32 + di;
            let nj = j as i32 + dj;
            if ni < 0 || ni >= m as i32 || nj < 0 || nj >= n as i32 {
                continue;
            }
            let (ni, nj) = (ni as usize, nj as usize);

            if grid[ni][nj] == 1 && !vis[ni][nj] {
                Self::dfs(grid, vis, ni, nj, m, n, dirs, cells, frontier, walls);
            } else if grid[ni][nj] == 0 {
                *walls += 1;
                frontier.insert(ni * n + nj);
            }
        }
    }
}
```
