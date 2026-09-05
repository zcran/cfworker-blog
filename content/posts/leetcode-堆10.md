---
title: "leetcode-堆10"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 为高尔夫比赛砍树

你被请来给一个要举办高尔夫比赛的树林砍树。树林由一个 m x n 的矩阵表示， 在这个矩阵中：

0 表示障碍，无法触碰
1 表示地面，可以行走
比 1 大的数 表示有树的单元格，可以行走，数值表示树的高度

每一步，你都可以向上、下、左、右四个方向之一移动一个单位，如果你站的地方有一棵树，那么你可以决定是否要砍倒它。

你需要按照树的高度从低向高砍掉所有的树，每砍过一颗树，该单元格的值变为 1（即变为地面）。

你将从 (0, 0) 点开始工作，返回你砍完所有树需要走的最小步数。 如果你无法砍完所有的树，返回 -1 。

可以保证的是，没有两棵树的高度是相同的，并且你至少需要砍倒一棵树。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 按树高从低到高依次砍伐，每次用 A* 搜索最短路径
    pub fn cut_off_tree(forest: Vec<Vec<i32>>) -> i32 {
        let (n, m) = (forest.len(), forest[0].len());
        if forest[0][0] == 0 {
            return -1;
        }

        // 收集所有树的位置，按高度排序
        let mut trees: Vec<(i32, usize, usize)> = Vec::new();
        for i in 0..n {
            for j in 0..m {
                if forest[i][j] > 1 {
                    trees.push((forest[i][j], i, j));
                }
            }
        }
        trees.sort_unstable();

        // 依次从当前位置走到下一棵树
        let mut pos = (0usize, 0usize);
        let mut total = 0;

        for &(_, tx, ty) in &trees {
            let d = Self::a_star(&forest, pos, (tx, ty));
            if d < 0 {
                return -1;
            }
            total += d;
            pos = (tx, ty);
        }

        total
    }

    /// A* 搜索：从 start 到 end 的最短步数，障碍为 0 不可走
    fn a_star(map: &[Vec<i32>], start: (usize, usize), end: (usize, usize)) -> i32 {
        let (n, m) = (map.len(), map[0].len());
        let mut dist = vec![vec![i32::MAX; m]; n];
        let mut heap = BinaryHeap::new();

        // 曼哈顿距离启发函数
        fn heuristic(a: (usize, usize), b: (usize, usize)) -> i32 {
            (a.0 as i32 - b.0 as i32).abs() + (a.1 as i32 - b.1 as i32).abs()
        }

        dist[start.0][start.1] = 0;
        heap.push(Reverse((heuristic(start, end), 0, start)));

        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while let Some(Reverse((_, step, (x, y)))) = heap.pop() {
            if (x, y) == end {
                return step;
            }
            if step > dist[x][y] {
                continue; // 已有更优路径
            }

            for &(dx, dy) in &DIRS {
                let nx = x as i32 + dx;
                let ny = y as i32 + dy;
                if nx < 0 || nx >= n as i32 || ny < 0 || ny >= m as i32 {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);
                if map[nx][ny] == 0 {
                    continue;
                }

                let next_step = step + 1;
                if next_step < dist[nx][ny] {
                    dist[nx][ny] = next_step;
                    let h = next_step + heuristic((nx, ny), end);
                    heap.push(Reverse((h, next_step, (nx, ny))));
                }
            }
        }

        -1
    }
}
```
