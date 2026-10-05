---
title: "leetcode-广度优先搜索50"
date: 2026-09-13T09:32:09+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 颜色填充

编写函数，实现许多图片编辑软件都支持的「颜色填充」功能。

待填充的图像用二维数组 image 表示，元素为初始颜色值。初始坐标点的行坐标为 sr 列坐标为 sc。需要填充的新颜色为 newColor 。

「周围区域」是指颜色相同且在上、下、左、右四个方向上存在相连情况的若干元素。

请用新颜色填充初始坐标点的周围区域，并返回填充后的图像。

```
impl Solution {
    pub fn flood_fill(mut image: Vec<Vec<i32>>, sr: i32, sc: i32, new_color: i32) -> Vec<Vec<i32>> {
        let (sr, sc) = (sr as usize, sc as usize);
        let old_color = image[sr][sc];

        // 起点颜色和新颜色相同，无需修改
        if old_color == new_color {
            return image;
        }

        let (n, m) = (image.len(), image[0].len());
        const DIRS: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        // 用栈做 DFS，原地染色，避免额外的 visited 数组
        let mut stack = vec![(sr, sc)];
        image[sr][sc] = new_color;

        while let Some((x, y)) = stack.pop() {
            for &(dx, dy) in &DIRS {
                let nx = x as isize + dx;
                let ny = y as isize + dy;
                if nx < 0 || ny < 0 || nx >= n as isize || ny >= m as isize {
                    continue;
                }
                let (nx, ny) = (nx as usize, ny as usize);
                if image[nx][ny] == old_color {
                    image[nx][ny] = new_color; // 入栈前染色，防止重复入栈
                    stack.push((nx, ny));
                }
            }
        }

        image
    }
}
```
