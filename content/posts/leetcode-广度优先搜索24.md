---
title: "leetcode-广度优先搜索24"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 逃离大迷宫

在一个 10^6 x 10^6 的网格中，每个网格上方格的坐标为 (x, y) 。

现在从源方格 source = [sx, sy] 开始出发，意图赶往目标方格 target = [tx, ty] 。数组 blocked 是封锁的方格列表，其中每个 blocked[i] = [xi, yi] 表示坐标为 (xi, yi) 的方格是禁止通行的。

每次移动，都可以走到网格中在四个方向上相邻的方格，只要该方格 不 在给出的封锁列表 blocked 上。同时，不允许走出网格。

只有在可以通过一系列的移动从源方格 source 到达目标方格 target 时才返回 true。否则，返回 false。


```
use std::collections::{HashMap, VecDeque};

impl Solution {
    pub fn is_escape_possible(blocked: Vec<Vec<i32>>, source: Vec<i32>, target: Vec<i32>) -> bool {
        // 把所有关心的坐标（障碍 + 起点 + 终点）都纳入考虑
        let mut nodes: Vec<&Vec<i32>> = blocked.iter().collect();
        nodes.push(&source);
        nodes.push(&target);

        // 分别取出所有 x、y 坐标
        let mut dx: Vec<i32> = nodes.iter().map(|p| p[0]).collect();
        let mut dy: Vec<i32> = nodes.iter().map(|p| p[1]).collect();
        dx.sort_unstable();
        dy.sort_unstable();

        // 坐标压缩：只保留出现过的坐标，相邻坐标间隔 > 1 时中间补一格，
        // 保证原本连通的空白区域在压缩后依然连通
        let (mut hash_x, mut hash_y) = (HashMap::new(), HashMap::new());

        let discrete = |coords, map: &mut HashMap<_, _>| -> usize {
            let (mut pos, mut prev) = (0usize, 0);
            for &k in coords {
                match k - prev {
                    0 => (),        // 重复坐标，位置不变
                    1 => pos += 1,  // 紧邻，直接 +1
                    _ => pos += 2,  // 有间隔，中间补一格
                }
                map.insert(k, pos);
                prev = k;
            }
            pos + 1 // 返回总长度
        };

        let mut rows = discrete(&dx, &mut hash_x);
        let mut cols = discrete(&dy, &mut hash_y);

        // 若最大坐标没有到达网格右下角边界，额外补一行/一列作为外部空间
        rows += if *dx.last().unwrap() != 999_999 { 1 } else { 0 };
        cols += if *dy.last().unwrap() != 999_999 { 1 } else { 0 };

        // 压缩后的网格：true 表示障碍
        let mut grid = vec![vec![false; cols]; rows];
        blocked.iter().for_each(|p| {
            grid[hash_x[&p[0]]][hash_y[&p[1]]] = true;
        });

        // 从 source 出发做 BFS，看能否到达 target
        let target_pos = (hash_x[&target[0]], hash_y[&target[1]]);
        let mut queue = VecDeque::new();
        queue.push_back((hash_x[&source[0]], hash_y[&source[1]]));

        while let Some(pos) = queue.pop_front() {
            if pos == target_pos {
                return true;
            }
            // 四个方向扩展
            for next in [
                (pos.0 - 1, pos.1),
                (pos.0 + 1, pos.1),
                (pos.0, pos.1 - 1),
                (pos.0, pos.1 + 1),
            ] {
                if next.0 < grid.len() && next.1 < grid[0].len() && !grid[next.0][next.1] {
                    queue.push_back(next);
                    grid[next.0][next.1] = true; // 标记已访问
                }
            }
        }
        false
    }
}
```
