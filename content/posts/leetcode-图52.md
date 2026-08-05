---
title: "leetcode-图52"
date: 2026-08-01T09:50:11+08:00
tags: ["leetcode", "图"]
draft: false
---


## 信物传送

欢迎各位勇者来到力扣城，本次试炼主题为「信物传送」。

本次试炼场地设有若干传送带，matrix[i][j] 表示第 i 行 j 列的传送带运作方向，"^","v","<",">" 这四种符号分别表示 上、下、左、右 四个方向。信物会随传送带的方向移动。勇者每一次施法操作，可临时变更一处传送带的方向，在物品经过后传送带恢复原方向。
![](../pic/1649835246-vfupSL-lcp.gif)

通关信物初始位于坐标 start处，勇者需要将其移动到坐标 end 处，请返回勇者施法操作的最少次数。

注意：

start 和 end 的格式均为 [i,j]

```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    /// 计算从 start 到 end 的最少施法次数
    ///
    /// # 算法思路
    /// 将每个格子视为节点，移动到相邻格子：
    /// - 如果方向与传送带一致，边权为 0（无需施法）
    /// - 如果方向不一致，边权为 1（需要施法一次）
    /// 使用 0-1 BFS（双端队列）或 Dijkstra 求最短路径
    ///
    /// # 优化
    /// 使用 0-1 BFS 替代 Dijkstra，时间复杂度 O(n*m)，比 O((n*m)log(n*m)) 更快
    pub fn conveyor_belt(matrix: Vec<String>, start: Vec<i32>, end: Vec<i32>) -> i32 {
        let m = matrix.len();
        let n = matrix[0].len();
        let matrix = matrix.iter().map(|s| s.as_bytes()).collect::<Vec<_>>();

        let (sx, sy) = (start[0] as usize, start[1] as usize);
        let (ex, ey) = (end[0] as usize, end[1] as usize);

        // 距离数组，初始化为最大值
        let mut dist = vec![vec![i32::MAX; n]; m];
        dist[sx][sy] = 0;

        // 使用 VecDeque 作为双端队列实现 0-1 BFS
        use std::collections::VecDeque;
        let mut deque = VecDeque::new();
        deque.push_back((sx, sy));

        // 方向映射：上、下、左、右对应的字符和位移
        const DIRS: [(i32, i32, u8); 4] = [(-1, 0, b'^'), (1, 0, b'v'), (0, -1, b'<'), (0, 1, b'>')];

        while let Some((x, y)) = deque.pop_front() {
            // 到达终点，直接返回（0-1 BFS 保证首次到达为最优）
            if x == ex && y == ey {
                return dist[x][y];
            }

            let current_dist = dist[x][y];
            let current_dir = matrix[x][y];

            // 遍历四个方向
            for &(dx, dy, dir_char) in &DIRS {
                let nx = x as i32 + dx;
                let ny = y as i32 + dy;

                // 检查边界
                if nx < 0 || nx >= m as i32 || ny < 0 || ny >= n as i32 {
                    continue;
                }

                let (nx, ny) = (nx as usize, ny as usize);
                // 边权：方向匹配为 0，否则为 1
                let cost = if current_dir == dir_char { 0 } else { 1 };
                let new_dist = current_dist + cost;

                if new_dist < dist[nx][ny] {
                    dist[nx][ny] = new_dist;
                    if cost == 0 {
                        deque.push_front((nx, ny)); // 0 权边放队首
                    } else {
                        deque.push_back((nx, ny)); // 1 权边放队尾
                    }
                }
            }
        }

        // 题目保证可达，这里不会执行
        dist[ex][ey]
    }
}
```
