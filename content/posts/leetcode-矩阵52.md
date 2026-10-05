---
title: "leetcode-矩阵52"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 查询网格图中每一列的宽度

给你一个下标从 0 开始的 m x n 整数矩阵 grid 。矩阵中某一列的宽度是这一列数字的最大 字符串长度 。

比方说，如果 grid = [[-10], [3], [12]] ，那么唯一一列的宽度是 3 ，因为 -10 的字符串长度为 3 。
请你返回一个大小为 n 的整数数组 ans ，其中 ans[i] 是第 i 列的宽度。

一个有 len 个数位的整数 x ，如果是非负数，那么 字符串长度 为 len ，否则为 len + 1 。


```
impl Solution {
    /// 求每列数字的最大字符串宽度。
    /// 宽度定义：非负数为其十进制位数，负数位数再 +1（负号占一位）。
    /// 技巧：取绝对值后统一按非负数数位计算；0 的位数为 1（`ilog10(0)` 未定义，需特判）。
    /// 用 `ilog10() + 1` 替代手写除 10 循环，编译为硬件指令，更快更简洁。
    pub fn find_column_width(grid: Vec<Vec<i32>>) -> Vec<i32> {
        let m = grid.len();
        let n = grid[0].len();
        let mut res = vec![0i32; n];

        for row in &grid {
            for (j, &val) in row.iter().enumerate() {
                // 数位：|val| 的十进制位数；负数额外加 1 表示负号
                let digits = if val == 0 {
                    1
                } else {
                    val.unsigned_abs().ilog10() + 1
                };
                let width = digits as i32 + if val < 0 { 1 } else { 0 };
                res[j] = res[j].max(width);
            }
        }

        res
    }
}
```
