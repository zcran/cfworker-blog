---
title: "leetcode-排序73"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 统计被覆盖的建筑

给你一个正整数 n，表示一个 n x n 的城市，同时给定一个二维数组 buildings，其中 buildings[i] = [x, y] 表示位于坐标 [x, y] 的一个 唯一 建筑。

如果一个建筑在四个方向（左、右、上、下）中每个方向上都至少存在一个建筑，则称该建筑 被覆盖 。

返回 被覆盖 的建筑数量。


```
impl Solution {
    pub fn count_covered_buildings(n: i32, buildings: Vec<Vec<i32>>) -> i32 {
        let size = n as usize + 1;

        // 对每一行/列，记录该行/列中建筑的最小与最大坐标
        let mut min_x_in_col = vec![n + 1; size]; // 同一 y 下的最小 x
        let mut max_x_in_col = vec![0; size];     // 同一 y 下的最大 x
        let mut min_y_in_row = vec![n + 1; size]; // 同一 x 下的最小 y
        let mut max_y_in_row = vec![0; size];     // 同一 x 下的最大 y

        for p in &buildings {
            let (x, y) = (p[0], p[1]);
            let (xu, yu) = (x as usize, y as usize);

            min_x_in_col[yu] = min_x_in_col[yu].min(x);
            max_x_in_col[yu] = max_x_in_col[yu].max(x);
            min_y_in_row[xu] = min_y_in_row[xu].min(y);
            max_y_in_row[xu] = max_y_in_row[xu].max(y);
        }

        // 若某建筑在上下左右四个方向都各有其它建筑，则被覆盖
        buildings
            .iter()
            .filter(|p| {
                let (x, y) = (p[0], p[1]);
                let (xu, yu) = (x as usize, y as usize);
                x > min_x_in_col[yu]
                    && x < max_x_in_col[yu]
                    && y > min_y_in_row[xu]
                    && y < max_y_in_row[xu]
            })
            .count() as i32
    }
}
```
