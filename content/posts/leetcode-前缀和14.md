---
title: "leetcode-前缀和14"
date: 2026-08-25T10:02:46+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 元素和为目标值的子矩阵数量

给出矩阵 matrix 和目标值 target，返回元素总和等于目标值的非空子矩阵的数量。

子矩阵 x1, y1, x2, y2 是满足 x1 <= x <= x2 且 y1 <= y <= y2 的所有单元 matrix[x][y] 的集合。

如果 (x1, y1, x2, y2) 和 (x1', y1', x2', y2') 两个子矩阵中部分坐标不同（如：x1 != x1'），那么这两个子矩阵也不同。


```
impl Solution {
    pub fn num_submatrix_sum_target(matrix: Vec<Vec<i32>>, target: i32) -> i32 {
        let rows = matrix.len();
        let cols = matrix[0].len();
        let mut ans = 0;

        // 枚举上边界
        for top in 0..rows {
            // col_sum[j] 表示当前上下边界之间第 j 列的和
            let mut col_sum = vec![0; cols];

            // 枚举下边界，逐渐累加列和
            for bottom in top..rows {
                // 使用 HashMap 统计前缀和出现次数
                let mut cnt = std::collections::HashMap::with_capacity(cols);
                let mut prefix = 0;
                cnt.insert(0, 1);

                for j in 0..cols {
                    col_sum[j] += matrix[bottom][j];
                    prefix += col_sum[j];
                    // 寻找 prefix - target 的出现次数
                    if let Some(&c) = cnt.get(&(prefix - target)) {
                        ans += c;
                    }
                    *cnt.entry(prefix).or_insert(0) += 1;
                }
            }
        }

        ans
    }
}
```
