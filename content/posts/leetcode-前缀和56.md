---
title: "leetcode-前缀和56"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 用邮票贴满网格图

给你一个 m x n 的二进制矩阵 grid ，每个格子要么为 0 （空）要么为 1 （被占据）。

给你邮票的尺寸为 stampHeight x stampWidth 。我们想将邮票贴进二进制矩阵中，且满足以下 限制 和 要求 ：

覆盖所有 空 格子。
不覆盖任何 被占据 的格子。
我们可以放入任意数目的邮票。
邮票可以相互有 重叠 部分。
邮票不允许 旋转 。
邮票必须完全在矩阵 内 。

如果在满足上述要求的前提下，可以放入邮票，请返回 true ，否则返回 false 。


```
impl Solution {
    pub fn possible_to_stamp(grid: Vec<Vec<i32>>, stamp_height: i32, stamp_width: i32) -> bool {
        let m = grid.len();
        let n = grid[0].len();
        let h = stamp_height as usize;
        let w = stamp_width as usize;

        // 如果邮票尺寸大于整个矩阵，无法覆盖所有空格（除非没有空格）
        if h > m || w > n {
            // 检查是否有空格，如果没有空格则返回 true
            for row in &grid {
                for &cell in row {
                    if cell == 0 {
                        return false;
                    }
                }
            }
            return true;
        }

        // 1. 二维前缀和：快速计算任意矩形内被占据格子的数量
        let mut prefix = vec![vec![0; n + 1]; m + 1];
        for i in 0..m {
            for j in 0..n {
                prefix[i + 1][j + 1] = prefix[i][j + 1] + prefix[i + 1][j] - prefix[i][j] + grid[i][j];
            }
        }

        // 2. 二维差分：标记所有可以放置邮票的位置
        // diff[i][j] 表示以 (i,j) 为左上角的矩形覆盖次数
        // 使用 (m+1) x (n+1) 大小，避免越界
        let mut diff = vec![vec![0; n + 1]; m + 1];

        for i in 0..=m - h {
            for j in 0..=n - w {
                // 检查以 (i,j) 为左上角，大小为 h x w 的矩形是否全部为空
                let occupied = prefix[i + h][j + w] - prefix[i][j + w] - prefix[i + h][j] + prefix[i][j];
                if occupied == 0 {
                    // 该位置可以放置邮票，使用差分标记覆盖区域
                    diff[i][j] += 1;
                    if j + w < n { diff[i][j + w] -= 1; }
                    if i + h < m { diff[i + h][j] -= 1; }
                    if i + h < m && j + w < n { diff[i + h][j + w] += 1; }
                }
            }
        }

        // 3. 还原差分矩阵，检查每个空格是否被至少一个邮票覆盖
        for i in 0..m {
            for j in 0..n {
                // 二维前缀和还原
                if i > 0 { diff[i][j] += diff[i - 1][j]; }
                if j > 0 { diff[i][j] += diff[i][j - 1]; }
                if i > 0 && j > 0 { diff[i][j] -= diff[i - 1][j - 1]; }

                // 如果当前格子是空的但未被任何邮票覆盖，则无法满足要求
                if grid[i][j] == 0 && diff[i][j] == 0 {
                    return false;
                }
            }
        }

        true
    }
}
```
