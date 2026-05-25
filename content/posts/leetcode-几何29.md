---
title: "leetcode-几何29"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 判断矩形的两个角落是否可达


给你两个正整数 xCorner 和 yCorner 和一个二维整数数组 circles ，其中 circles[i] = [xi, yi, ri] 表示一个圆心在 (xi, yi) 半径为 ri 的圆。

坐标平面内有一个左下角在原点，右上角在 (xCorner, yCorner) 的矩形。你需要判断是否存在一条从左下角到右上角的路径满足：路径 完全 在矩形内部，不会 触碰或者经过 任何 圆的内部和边界，同时 只 在起点和终点接触到矩形。

如果存在这样的路径，请你返回 true ，否则返回 false 。



```
impl Solution {
    /// 判断是否存在一条从 (0,0) 到 (x_corner, y_corner) 的路径，不触碰任何圆。
    pub fn can_reach_corner(x_corner: i32, y_corner: i32, circles: Vec<Vec<i32>>) -> bool {
        // 将圆数据转为元组，提升访问效率
        let circles: Vec<(i32, i32, i32)> = circles
            .into_iter()
            .map(|c| (c[0], c[1], c[2]))
            .collect();
        let n = circles.len();

        // ---------- 纯函数几何判断 ----------
        fn point_in_circle(px: i32, py: i32, (cx, cy, r): (i32, i32, i32)) -> bool {
            let dx = cx as i128 - px as i128;
            let dy = cy as i128 - py as i128;
            let r = r as i128;
            dx * dx + dy * dy <= r * r
        }

        fn intersects_top_left(x_corner: i32, y_corner: i32, (x, y, r): (i32, i32, i32)) -> bool {
            (x.abs() <= r && (0..=y_corner).contains(&y))
                || (x >= 0 && x <= x_corner && (y - y_corner).abs() <= r)
                || point_in_circle(0, y_corner, (x, y, r))
        }

        fn intersects_bottom_right(x_corner: i32, y_corner: i32, (x, y, r): (i32, i32, i32)) -> bool {
            (y.abs() <= r && (0..=x_corner).contains(&x))
                || (y >= 0 && y <= y_corner && (x - x_corner).abs() <= r)
                || point_in_circle(x_corner, 0, (x, y, r))
        }

        fn intersects_in_rectangle(x_corner: i32, y_corner: i32,
                                   (x1, y1, r1): (i32, i32, i32),
                                   (x2, y2, r2): (i32, i32, i32)) -> bool {
            let dx = x1 as i128 - x2 as i128;
            let dy = y1 as i128 - y2 as i128;
            let r_sum = r1 as i128 + r2 as i128;
            dx * dx + dy * dy <= r_sum * r_sum
                && (x1 as i128 * r2 as i128 + x2 as i128 * r1 as i128) < r_sum * x_corner as i128
                && (y1 as i128 * r2 as i128 + y2 as i128 * r1 as i128) < r_sum * y_corner as i128
        }

        // ---------- 起点或终点被覆盖则直接失败 ----------
        for &circle in &circles {
            if point_in_circle(0, 0, circle) || point_in_circle(x_corner, y_corner, circle) {
                return false;
            }
        }

        let mut visited = vec![false; n];

        // DFS：从圆 i 出发，是否能到达与右下边界相交的圆
        fn dfs(i: usize,
               circles: &[(i32, i32, i32)],
               x_corner: i32,
               y_corner: i32,
               visited: &mut [bool]) -> bool {
            let circle = circles[i];
            if intersects_bottom_right(x_corner, y_corner, circle) {
                return true;
            }
            visited[i] = true;
            for (j, &other) in circles.iter().enumerate() {
                if !visited[j] && intersects_in_rectangle(x_corner, y_corner, circle, other) {
                    if dfs(j, circles, x_corner, y_corner, visited) {
                        return true;
                    }
                }
            }
            false
        }

        // 从每个与左上边界相交的圆出发进行 DFS
        for (i, &circle) in circles.iter().enumerate() {
            if !visited[i] && intersects_top_left(x_corner, y_corner, circle) {
                if dfs(i, &circles, x_corner, y_corner, &mut visited) {
                    return false; // 发现阻断链，无法到达角落
                }
            }
        }
        true // 没有阻断，可以到达
    }
}
```
