---
title: "leetcode-模拟140"
date: 2026-08-08T11:31:17+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 兰顿蚂蚁

一只蚂蚁坐在由白色和黑色方格构成的无限网格上。开始时，网格全白，蚂蚁面向右侧。每行走一步，蚂蚁执行以下操作。

(1) 如果在白色方格上，则翻转方格的颜色，向右(顺时针)转 90 度，并向前移动一个单位。
(2) 如果在黑色方格上，则翻转方格的颜色，向左(逆时针方向)转 90 度，并向前移动一个单位。

编写程序来模拟蚂蚁执行的前 K 个动作，并返回最终的网格。

网格由数组表示，每个元素是一个字符串，代表网格中的一行，黑色方格由 'X' 表示，白色方格由 '_' 表示，蚂蚁所在的位置由 'L', 'U', 'R', 'D' 表示，分别表示蚂蚁 左、上、右、下 的朝向。只需要返回能够包含蚂蚁走过的所有方格的最小矩形。

```
use std::collections::HashSet;

impl Solution {
    /// 模拟兰顿蚂蚁前 K 步，返回最终网格
    pub fn print_k_moves(k: i32) -> Vec<String> {
        let (mut x, mut y, mut d) = (0i32, 0i32, 0usize);
        const DX: [i32; 4] = [1, 0, -1, 0];
        const DY: [i32; 4] = [0, -1, 0, 1];
        const DIR: [char; 4] = ['R', 'U', 'L', 'D'];

        let mut black = HashSet::new();
        let (mut min_x, mut max_x, mut min_y, mut max_y) = (0, 0, 0, 0);

        for _ in 0..k {
            // remove 返回 true 表示原来是黑色（在集合中）
            if black.remove(&(x, y)) {
                d = (d + 1) % 4; // 左转
            } else {
                black.insert((x, y));
                d = (d + 3) % 4; // 右转
            }
            x += DX[d];
            y += DY[d];

            min_x = min_x.min(x);
            max_x = max_x.max(x);
            min_y = min_y.min(y);
            max_y = max_y.max(y);
        }

        let w = (max_x - min_x + 1) as usize;
        let h = (max_y - min_y + 1) as usize;
        let mut grid = vec![vec!['_'; w]; h];

        for &(bx, by) in &black {
            grid[(by - min_y) as usize][(bx - min_x) as usize] = 'X';
        }
        grid[(y - min_y) as usize][(x - min_x) as usize] = DIR[d];

        grid.into_iter().map(|row| row.into_iter().collect()).collect()
    }
}
```
