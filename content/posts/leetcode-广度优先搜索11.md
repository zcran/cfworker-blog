---
title: "leetcode-广度优先搜索11"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 01 矩阵

给定一个由 0 和 1 组成的矩阵 mat ，请输出一个大小相同的矩阵，其中每一个格子是 mat 中对应位置元素到最近的 0 的距离。

两个相邻元素间的距离为 1 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn update_matrix(mat: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let m = mat.len();
        let n = mat[0].len();

        // dist 同时充当访问标记：-1 表示未访问，>= 0 表示到最近 0 的距离
        let mut dist = vec![vec![-1i32; n]; m];
        let mut queue = VecDeque::new();

        // 多源 BFS：所有 0 作为起点同时入队
        for i in 0..m {
            for j in 0..n {
                if mat[i][j] == 0 {
                    dist[i][j] = 0;
                    queue.push_back((i, j));
                }
            }
        }

        // 向四个方向扩散，距离逐层 +1
        while let Some((i, j)) = queue.pop_front() {
            let d = dist[i][j];
            for (di, dj) in [(1, 0), (-1, 0), (0, 1), (0, -1)] {
                let x = i as isize + di;
                let y = j as isize + dj;
                if x >= 0 && x < m as isize && y >= 0 && y < n as isize {
                    let (x, y) = (x as usize, y as usize);
                    if dist[x][y] == -1 {
                        dist[x][y] = d + 1;
                        queue.push_back((x, y));
                    }
                }
            }
        }

        dist
    }
}
```
