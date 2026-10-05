---
title: "leetcode-广度优先搜索47"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 多源图像渲染

给你两个整数 n 和 m，分别表示一个网格的行数和列数。

同时给你一个二维整数数组 sources，其中 sources[i] = [ri, ci, colori] 表示单元格 (ri, ci) 初始被涂上颜色 colori。所有其他单元格初始均未着色，用 0 表示。

在每一单位时间中，所有当前已着色的单元格都会将其颜色向上下左右四个方向扩散到所有相邻的 未着色 单元格。所有扩散同时发生。

如果 多个 颜色在同一时间步到达同一个未着色单元格，该单元格将采用具有 最大 值的颜色。

这个过程持续进行，直到没有更多的单元格可以被着色。

返回一个二维整数数组，表示网格的最终状态，其中每个单元格包含其最终的颜色。


```
use std::collections::VecDeque;

impl Solution {
    pub fn color_grid(n: i32, m: i32, sources: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let (n, m) = (n as usize, m as usize);

        // 按颜色从大到小排序，保证多色同时到达时，大颜色先着色
        let mut sources = sources;
        sources.sort_unstable_by(|a, b| b[2].cmp(&a[2]));

        let mut ans = vec![vec![0i32; m]; n];
        let mut queue = VecDeque::new();

        // 初始化所有源点：直接涂色并加入队列
        for p in &sources {
            let (r, c, color) = (p[0] as usize, p[1] as usize, p[2]);
            ans[r][c] = color;
            queue.push_back((r, c));
        }

        const DIRS: [(isize, isize); 4] = [(0, -1), (0, 1), (-1, 0), (1, 0)];

        // BFS：同时扩散所有已着色单元格
        while let Some((i, j)) = queue.pop_front() {
            for &(dx, dy) in &DIRS {
                let x = i as isize + dx;
                let y = j as isize + dy;
                if x < 0 || y < 0 || x >= n as isize || y >= m as isize {
                    continue;
                }
                let (x, y) = (x as usize, y as usize);
                // 只处理未着色格子，避免被小颜色覆盖
                if ans[x][y] == 0 {
                    ans[x][y] = ans[i][j];
                    queue.push_back((x, y));
                }
            }
        }

        ans
    }
}
```
