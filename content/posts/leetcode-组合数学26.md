---
title: "leetcode-组合数学26"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 直角三角形


给你一个二维 boolean 矩阵 grid 。

如果 grid 的 3 个元素的集合中，一个元素与另一个元素在 同一行，并且与第三个元素在 同一列，则该集合是一个 直角三角形。3 个元素 不必 彼此相邻。

请你返回使用 grid 中的 3 个元素可以构建的 直角三角形 数目，且满足 3 个元素值 都 为 1 。



```
impl Solution {
    /// 计算所有由三个值为 1 的格子组成的直角三角形的个数。
    /// 每个值为 1 的点作为直角顶点，贡献为 (同行1的个数 - 1) * (同列1的个数 - 1)。
    /// 优化：预先计算每列的贡献（列中1的个数 - 1），然后对每行：
    ///   - 统计该行中1的个数 row_sum
    ///   - 累加该行中每个1所在列的贡献 col_contrib
    ///   - 该行贡献 = (row_sum - 1) * col_contrib_sum
    pub fn number_of_right_triangles(grid: Vec<Vec<i32>>) -> i64 {
        // 空网格处理
        if grid.is_empty() {
            return 0;
        }
        let cols = grid[0].len();

        // 计算每一列中 1 的个数减 1（即该列可形成的直角边数量）
        let col_contrib: Vec<i32> = (0..cols)
            .map(|j| grid.iter().map(|row| row[j]).sum::<i32>() - 1)
            .collect();

        // 遍历每一行，累计结果
        grid.iter()
            .map(|row| {
                let row_sum = row.iter().sum::<i32>();          // 该行中 1 的个数
                let col_sum: i32 = row.iter()
                    .zip(&col_contrib)
                    .filter_map(|(&val, &c)| if val == 1 { Some(c) } else { None })
                    .sum();                                   // 该行中所有 1 对应的列贡献之和
                (row_sum - 1) as i64 * col_sum as i64
            })
            .sum()
    }
}
```
