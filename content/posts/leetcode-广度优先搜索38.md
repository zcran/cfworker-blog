---
title: "leetcode-广度优先搜索38"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 找到所有的农场组

给你一个下标从 0 开始，大小为 m x n 的二进制矩阵 land ，其中 0 表示一单位的森林土地，1 表示一单位的农场土地。

为了让农场保持有序，农场土地之间以矩形的 农场组 的形式存在。每一个农场组都 仅 包含农场土地。且题目保证不会有两个农场组相邻，也就是说一个农场组中的任何一块土地都 不会 与另一个农场组的任何一块土地在四个方向上相邻。

land 可以用坐标系统表示，其中 land 左上角坐标为 (0, 0) ，右下角坐标为 (m-1, n-1) 。请你找到所有 农场组 最左上角和最右下角的坐标。一个左上角坐标为 (r1, c1) 且右下角坐标为 (r2, c2) 的 农场组 用长度为 4 的数组 [r1, c1, r2, c2] 表示。

请你返回一个二维数组，它包含若干个长度为 4 的子数组，每个子数组表示 land 中的一个 农场组 。如果没有任何农场组，请你返回一个空数组。可以以 任意顺序 返回所有农场组。


```
impl Solution {
    pub fn find_farmland(land: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let (rows, cols) = (land.len(), land[0].len());
        let mut visited = vec![vec![false; cols]; rows];
        let mut result = Vec::new();

        for i in 0..rows {
            for j in 0..cols {
                if land[i][j] == 0 || visited[i][j] {
                    continue;
                }

                // 题目保证农场组是矩形且互不相邻，
                // 因此从左上角出发，向下、向右扫描即可确定右下角
                let mut r2 = i;
                while r2 + 1 < rows && land[r2 + 1][j] == 1 {
                    r2 += 1;
                }
                let mut c2 = j;
                while c2 + 1 < cols && land[i][c2 + 1] == 1 {
                    c2 += 1;
                }

                // 标记整个矩形为已访问
                for r in i..=r2 {
                    for c in j..=c2 {
                        visited[r][c] = true;
                    }
                }

                result.push(vec![i as i32, j as i32, r2 as i32, c2 as i32]);
            }
        }

        result
    }
}
```
