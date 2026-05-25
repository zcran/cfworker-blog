---
title: "leetcode-位掩码32"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 选择矩阵中单元格的最大得分


给你一个由正整数构成的二维矩阵 grid。

你需要从矩阵中选择 一个或多个 单元格，选中的单元格应满足以下条件：

· 所选单元格中的任意两个单元格都不会处于矩阵的 同一行。
· 所选单元格的值 互不相同。

你的得分为所选单元格值的总和。

返回你能获得的 最大 得分。

```
impl Solution {
    pub fn max_score(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        if m == 0 {
            return 0;
        }

        // 1. 离散化所有数值
        let mut all_vals: Vec<i32> = grid.iter().flat_map(|row| row.iter().copied()).collect();
        all_vals.sort_unstable();
        all_vals.dedup();
        let val_to_idx: std::collections::HashMap<i32, usize> = all_vals
            .iter()
            .enumerate()
            .map(|(i, &v)| (v, i))
            .collect();
        let val_cnt = all_vals.len();

        // 2. 每行：去重、降序排序，转换为数值索引
        let rows: Vec<Vec<usize>> = grid
            .iter()
            .map(|row| {
                let mut uniq: Vec<i32> = row.iter().copied().collect();
                uniq.sort_unstable_by(|a, b| b.cmp(a)); // 降序
                uniq.dedup();
                uniq.into_iter().map(|v| val_to_idx[&v]).collect()
            })
            .collect();

        let global_max = *all_vals.last().unwrap_or(&0);
        let mut used = vec![false; val_cnt];
        let mut best = 0;

        /// 深度优先搜索，每行可选或不选，所选数值不可重复
        fn dfs(
            idx: usize,          // 当前处理的行索引
            sum: i32,            // 当前总和
            used: &mut [bool],   // 数值使用状态
            rows: &[Vec<usize>], // 每行可选的数值索引（已去重降序）
            vals: &[i32],        // 原始数值数组
            global_max: i32,     // 全局最大数值
            m: usize,            // 总行数
            best: &mut i32,      // 最优解
        ) {
            if idx == m {
                *best = (*best).max(sum);
                return;
            }

            // 乐观剪枝：剩余行数 × 全局最大值 + 当前和 ≤ 最优解，则不可能更优
            let remain = (m - idx) as i32;
            if sum + remain * global_max <= *best {
                return;
            }

            // 选择1：跳过当前行
            dfs(idx + 1, sum, used, rows, vals, global_max, m, best);

            // 选择2：尝试选当前行的一个未使用数值（按降序顺序尝试，剪枝更有效）
            for &val_idx in &rows[idx] {
                if !used[val_idx] {
                    used[val_idx] = true;
                    dfs(idx + 1, sum + vals[val_idx], used, rows, vals, global_max, m, best);
                    used[val_idx] = false;
                }
            }
        }

        dfs(0, 0, &mut used, &rows, &all_vals, global_max, m, &mut best);
        best
    }
}
```
