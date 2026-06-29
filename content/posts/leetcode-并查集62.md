---
title: "leetcode-并查集62"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 最大划分因子

给你一个二维整数数组 points，其中 points[i] = [xi, yi] 表示笛卡尔平面上第 i 个点的坐标。

两个点 points[i] = [xi, yi] 和 points[j] = [xj, yj] 之间的 曼哈顿距离 是 |xi - xj| + |yi - yj|。

将这 n 个点分成 恰好两个非空 的组。一个划分的 划分因子 是位于同一组内的所有无序点对之间 最小 的曼哈顿距离。

返回所有有效划分中 最大 可能的 划分因子 。

注意: 大小为 1 的组不存在任何组内点对。当 n = 2 时（两个组大小都为 1），没有组内点对，划分因子为 0。




```
impl Solution {
    pub fn max_partition_factor(points: Vec<Vec<i32>>) -> i32 {
        let n = points.len();

        // n = 2 时两个组各有一个点，没有组内点对
        if n == 2 {
            return 0;
        }

        // 检查是否可以将点分成两组，使得同一组内任意两点的曼哈顿距离 >= low
        fn can_partition(points: &Vec<Vec<i32>>, n: usize, low: i32) -> bool {
            let mut color = vec![0; n]; // 0: 未染色, 1: 组A, -1: 组B

            // DFS 染色，检查是否为二分图
            fn dfs(
                points: &Vec<Vec<i32>>,
                color: &mut Vec<i32>,
                x: usize,
                c: i32,
                low: i32,
                n: usize,
            ) -> bool {
                color[x] = c;
                let (x1, y1) = (points[x][0], points[x][1]);

                for y in 0..n {
                    if y == x {
                        continue;
                    }
                    let (x2, y2) = (points[y][0], points[y][1]);
                    let dist = (x1 - x2).abs() + (y1 - y2).abs();

                    // 距离 >= low 的点可以放在同一组，不需要处理
                    if dist >= low {
                        continue;
                    }

                    // 距离 < low 的点必须放在不同组
                    if color[y] == c {
                        return false; // 冲突，不是二分图
                    }
                    if color[y] == 0 && !dfs(points, color, y, -c, low, n) {
                        return false;
                    }
                }
                true
            }

            // 处理所有连通分量
            for i in 0..n {
                if color[i] == 0 && !dfs(points, &mut color, i, 1, low, n) {
                    return false;
                }
            }
            true
        }

        // 计算最大可能距离作为二分搜索的上界
        let mut max_dist = 0;
        for i in 0..n {
            for j in i + 1..n {
                let dist = (points[i][0] - points[j][0]).abs() + (points[i][1] - points[j][1]).abs();
                max_dist = max_dist.max(dist);
            }
        }

        // 二分查找最大的可行划分因子
        let mut left = 0;
        let mut right = max_dist + 1;
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if can_partition(&points, n, mid) {
                left = mid;
            } else {
                right = mid;
            }
        }

        left
    }
}
```
