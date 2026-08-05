---
title: "leetcode-并查集72"
date: 2026-06-25T11:22:24+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 水域大小

你有一个用于表示一片土地的整数矩阵land，该矩阵中每个点的值代表对应地点的海拔高度。若值为0则表示水域。由垂直、水平或对角连接的水域为池塘。池塘的大小是指相连接的水域的个数。编写一个方法来计算矩阵中所有池塘的大小，返回值需要从小到大排序。


```
impl Solution {
    /// 计算矩阵中所有池塘的大小（8方向连通），返回值从小到大排序
    pub fn pond_sizes(mut land: Vec<Vec<i32>>) -> Vec<i32> {
        let rows = land.len();
        let cols = land[0].len();
        let mut sizes = Vec::new();
        let mut stack = Vec::new();

        for i in 0..rows {
            for j in 0..cols {
                if land[i][j] != 0 {
                    continue;
                }

                // BFS/DFS 迭代搜索池塘
                land[i][j] = -1; // 标记已访问
                stack.push((i, j));
                let mut area = 0;

                while let Some((x, y)) = stack.pop() {
                    area += 1;

                    // 检查 8 个邻居（使用 saturating 避免溢出）
                    let x_start = x.saturating_sub(1);
                    let x_end = (x + 1).min(rows - 1);
                    let y_start = y.saturating_sub(1);
                    let y_end = (y + 1).min(cols - 1);

                    for nx in x_start..=x_end {
                        for ny in y_start..=y_end {
                            if land[nx][ny] == 0 {
                                land[nx][ny] = -1;
                                stack.push((nx, ny));
                            }
                        }
                    }
                }

                sizes.push(area);
            }
        }

        sizes.sort_unstable();
        sizes
    }
}
```
