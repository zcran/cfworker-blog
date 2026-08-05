---
title: "leetcode-枚举59"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 构造相同颜色的正方形

给你一个二维 3 x 3 的矩阵 grid ，每个格子都是一个字符，要么是 'B' ，要么是 'W' 。字符 'W' 表示白色，字符 'B' 表示黑色。

你的任务是改变 至多一个 格子的颜色，使得矩阵中存在一个 2 x 2 颜色完全相同的正方形。

如果可以得到一个相同颜色的 2 x 2 正方形，那么返回 true ，否则返回 false 。


```
impl Solution {
    pub fn can_make_square(grid: Vec<Vec<char>>) -> bool {
        // 遍历所有可能的 2x2 子正方形（共 4 个）
        for i in 0..2 {
            for j in 0..2 {
                // 统计当前 2x2 子正方形中 'B' 的数量
                let mut black_count = 0;
                for r in 0..2 {
                    for c in 0..2 {
                        if grid[i + r][j + c] == 'B' {
                            black_count += 1;
                        }
                    }
                }

                // 如果 'B' 的数量不是 2，说明可以通过改变至多一个格子使其全相同
                // count=0 或 1：变为全 'W'（改变 count 个格子）
                // count=3 或 4：变为全 'B'（改变 4-count 个格子）
                // count=2 时，无论改变哪个格子都会变成 3 个相同 + 1 个不同，无法全相同
                if black_count != 2 {
                    return true;
                }
            }
        }
        false
    }
}
```
