---
title: "leetcode-前缀和139"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---

## 矩阵中最大共享路径和

给你一个 m x n 的整数矩阵 grid 。

两个玩家在矩阵中移动：

玩家 1 从左上角单元格 (0, 0) 出发，只能向右或向下移动。他们的目的地是右下角单元格 (m - 1, n - 1) 。
玩家 2 从左下角单元格 (m - 1, 0) 出发，只能向右或向上移动。他们的目的地是右上角单元格 (0, n - 1) 。

每个玩家必须选择一条从各自起始单元格到目的地的有效路径。

如果一个单元格属于 两条 被选中的路径，则称该单元格为 共享 单元格。

返回一个整数，表示所有 共享 单元格的值的 最大 可能总和。


```
impl Solution {
    pub fn max_score(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let mut ans = i32::MIN;

        // 辅助函数：计算长度为 >= 2 的最大子数组和
        fn max_subarray_at_least_two(nums: &[i32]) -> i32 {
            let mut ans = i32::MIN;
            let mut f = nums[0];
            for &x in &nums[1..] {
                ans = ans.max(f + x); // 保证至少两个元素
                f = f.max(0) + x;
            }
            ans
        }

        // 1. 长度为 1 的子数组（不能位于边界上）
        for i in 1..m - 1 {
            for j in 1..n - 1 {
                ans = ans.max(grid[i][j]);
            }
        }

        // 2. 每行的长度 >= 2 的最大子数组和
        for row in &grid {
            ans = ans.max(max_subarray_at_least_two(row));
        }

        // 3. 每列的长度 >= 2 的最大子数组和
        for j in 0..n {
            let col: Vec<i32> = (0..m).map(|i| grid[i][j]).collect();
            ans = ans.max(max_subarray_at_least_two(&col));
        }

        ans
    }
}
```
