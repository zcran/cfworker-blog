---
title: "leetcode-哈希表62"
date: 2026-10-09T20:07:59+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 查询后矩阵的和

给你一个整数 n 和一个下标从 0 开始的 二维数组 queries ，其中 queries[i] = [typei, indexi, vali] 。

一开始，给你一个下标从 0 开始的 n x n 矩阵，所有元素均为 0 。每一个查询，你需要执行以下操作之一：

如果 typei == 0 ，将第 indexi 行的元素全部修改为 vali ，覆盖任何之前的值。
如果 typei == 1 ，将第 indexi 列的元素全部修改为 vali ，覆盖任何之前的值。

请你执行完所有查询以后，返回矩阵中所有整数的和。


```
impl Solution {
    pub fn matrix_sum_queries(n: i32, queries: Vec<Vec<i32>>) -> i64 {
        let n = n as i64;

        // 倒序处理：后执行的查询会覆盖先执行的，因此倒序时首次遇到的即为最终生效的
        // row_set[i] / col_set[i] 记录该行/列是否已被后续查询覆盖过
        let mut row_set = vec![false; n as usize];
        let mut col_set = vec![false; n as usize];

        let mut row_cnt = 0i64; // 已确定的行数
        let mut col_cnt = 0i64; // 已确定的列数
        let mut sum = 0i64;

        for q in queries.iter().rev() {
            let (t, idx, val) = (q[0], q[1] as usize, q[2] as i64);

            if t == 0 {
                // 该行已被更晚的查询覆盖，跳过
                if row_set[idx] {
                    continue;
                }
                row_set[idx] = true;
                // 该行中尚未被列查询覆盖的单元格数为 n - col_cnt
                sum += val * (n - col_cnt);
                row_cnt += 1;
            } else {
                if col_set[idx] {
                    continue;
                }
                col_set[idx] = true;
                sum += val * (n - row_cnt);
                col_cnt += 1;
            }

            // 所有行列都已被覆盖，后续查询不可能再影响结果
            if row_cnt == n && col_cnt == n {
                break;
            }
        }

        sum
    }
}
```
