---
title: "leetcode-记忆化37"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 自行车炫技赛场

「力扣挑战赛」中 N*M 大小的自行车炫技赛场的场地由一片连绵起伏的上下坡组成，场地的高度值记录于二维数组 terrain 中，场地的减速值记录于二维数组 obstacle 中。

若选手骑着自行车从高度为 h1 且减速值为 o1 的位置到高度为 h2 且减速值为 o2 的相邻位置（上下左右四个方向），速度变化值为 h1-h2-o2（负值减速，正值增速）。

选手初始位于坐标 position 处且初始速度为 1，请问选手可以刚好到其他哪些位置时速度依旧为 1。请以二维数组形式返回这些位置。若有多个位置则按行坐标升序排列，若有多个位置行坐标相同则按列坐标升序排列。

注意： 骑行过程中速度不能为零或负值

```
use std::collections::{HashSet, VecDeque};

impl Solution {
    /// 计算所有能在速度变为 1 时返回的坐标（不包含起点）。
    ///
    /// 自行车从 `position` 出发，初始速度为 1。每步可以移动到四邻域，速度变化遵循：
    /// 新速度 = 当前速度 + 当前格子地形 - 目标格子地形 - 目标格子障碍物。
    /// 只有新速度 > 0 才能移动。BFS 搜索所有可达的 (坐标, 速度) 状态，
    /// 当速度回到 1 且坐标不是起点时，记录该坐标。
    pub fn bicycle_yard(
        position: Vec<i32>,
        terrain: Vec<Vec<i32>>,
        obstacle: Vec<Vec<i32>>,
    ) -> Vec<Vec<i32>> {
        let (m, n) = (terrain.len(), terrain[0].len());
        let (start_i, start_j) = (position[0] as usize, position[1] as usize);

        // 使用 HashSet 存储已访问状态 (i, j, speed)
        let mut visited = HashSet::new();
        let mut queue = VecDeque::new();
        queue.push_back((start_i, start_j, 1)); // (行, 列, 速度)
        visited.insert((start_i, start_j, 1));

        let mut ans = Vec::new();

        // 四个方向：上下左右 (行偏移, 列偏移)
        let directions = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        while let Some((i, j, speed)) = queue.pop_front() {
            // 如果速度恰好为 1 且不是起点，记录答案
            if speed == 1 && (i, j) != (start_i, start_j) {
                ans.push(vec![i as i32, j as i32]);
            }

            // 遍历四个方向
            for (di, dj) in directions {
                let ni = i as i32 + di;
                let nj = j as i32 + dj;
                // 边界检查
                if ni < 0 || nj < 0 || ni >= m as i32 || nj >= n as i32 {
                    continue;
                }
                let (ni, nj) = (ni as usize, nj as usize);

                // 计算新速度：当前地形 - 目标地形 - 目标障碍 + 当前速度
                let new_speed = speed + terrain[i][j] - terrain[ni][nj] - obstacle[ni][nj];
                if new_speed > 0 && visited.insert((ni, nj, new_speed)) {
                    queue.push_back((ni, nj, new_speed));
                }
            }
        }

        // 按坐标升序排序（先按行，再按列）
        ans.sort_unstable();
        ans
    }
}
```
