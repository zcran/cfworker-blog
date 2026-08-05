---
title: "leetcode-回溯62"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 被列覆盖的最多行数

给你一个下标从 0 开始、大小为 m x n 的二进制矩阵 matrix ；另给你一个整数 numSelect，表示你必须从 matrix 中选择的 不同 列的数量。

如果一行中所有的 1 都被你选中的列所覆盖，则认为这一行被 覆盖 了。

形式上，假设 s = {c1, c2, ...., cnumSelect} 是你选择的列的集合。对于矩阵中的某一行 row ，如果满足下述条件，则认为这一行被集合 s 覆盖：

· 对于满足 matrix[row][col] == 1 的每个单元格 matrix[row][col]（0 <= col <= n - 1），col 均存在于 s 中，或者
· row 中 不存在 值为 1 的单元格。

你需要从矩阵中选出 numSelect 个列，使集合覆盖的行数最大化。

返回一个整数，表示可以由 numSelect 列构成的集合 覆盖 的 最大行数 。


```
impl Solution {
    pub fn maximum_rows(matrix: Vec<Vec<i32>>, num_select: i32) -> i32 {
        let n = matrix[0].len();
        let k = num_select as u32;

        // 将每行转换为位掩码
        let rows: Vec<u32> = matrix
            .iter()
            .map(|row| {
                row.iter()
                    .enumerate()
                    .fold(0, |mask, (col, &val)| mask | ((val as u32) << col))
            })
            .collect();

        let mut ans = 0;

        // 使用 u32 类型的范围，避免类型推断错误
        for mask in 0u32..(1 << n) {
            if mask.count_ones() == k {
                let covered = rows.iter().filter(|&&r| (r & mask) == r).count() as i32;
                ans = ans.max(covered);
            }
        }

        ans
    }
}
```
